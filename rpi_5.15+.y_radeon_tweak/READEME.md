Based on Coreforge's linux [repo](https://github.com/Coreforge/linux/commit/b3134bd0511c2676a96d0d9c018cc035f6877360)

Kernel Version is `5.10.33`

`include/drm/ttm/ttm_tt.c`
add@line 130
```
void ttm_tt_destroy_common(struct ttm_device *bdev, struct ttm_tt *ttm)
{
	ttm_tt_unpopulate(bdev, ttm);

	if (ttm->swap_storage)
		fput(ttm->swap_storage);

	ttm->swap_storage = NULL;
}
EXPORT_SYMBOL(ttm_tt_destroy_common);
```

`drivers/gpu/drm/ttm/ttm_tt.h`
add@line 89
```
#define TTM_TT_FLAG_EXTERNAL_MAPPABLE	(1 << 3)
```

full `drivers/gpu/drm/ttm/ttm_tt.c`(tested in 5.15 & 5.16)

```c
/* SPDX-License-Identifier: GPL-2.0 OR MIT */
/**************************************************************************
 *
 * Copyright (c) 2006-2009 VMware, Inc., Palo Alto, CA., USA
 * All Rights Reserved.
 *
 * Permission is hereby granted, free of charge, to any person obtaining a
 * copy of this software and associated documentation files (the
 * "Software"), to deal in the Software without restriction, including
 * without limitation the rights to use, copy, modify, merge, publish,
 * distribute, sub license, and/or sell copies of the Software, and to
 * permit persons to whom the Software is furnished to do so, subject to
 * the following conditions:
 *
 * The above copyright notice and this permission notice (including the
 * next paragraph) shall be included in all copies or substantial portions
 * of the Software.
 *
 * THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
 * IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
 * FITNESS FOR A PARTICULAR PURPOSE AND NON-INFRINGEMENT. IN NO EVENT SHALL
 * THE COPYRIGHT HOLDERS, AUTHORS AND/OR ITS SUPPLIERS BE LIABLE FOR ANY CLAIM,
 * DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR
 * OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE
 * USE OR OTHER DEALINGS IN THE SOFTWARE.
 *
 **************************************************************************/
/*
 * Authors: Thomas Hellstrom <thellstrom-at-vmware-dot-com>
 */

#define pr_fmt(fmt) "[TTM] " fmt

#include <linux/sched.h>
#include <linux/shmem_fs.h>
#include <linux/file.h>
#include <linux/module.h>
#include <drm/drm_cache.h>
#include <drm/ttm/ttm_bo_driver.h>

#include "ttm_module.h"

static unsigned long ttm_pages_limit;

MODULE_PARM_DESC(pages_limit, "Limit for the allocated pages");
module_param_named(pages_limit, ttm_pages_limit, ulong, 0644);

static unsigned long ttm_dma32_pages_limit;

MODULE_PARM_DESC(dma32_pages_limit, "Limit for the allocated DMA32 pages");
module_param_named(dma32_pages_limit, ttm_dma32_pages_limit, ulong, 0644);

static atomic_long_t ttm_pages_allocated;
static atomic_long_t ttm_dma32_pages_allocated;

/*
 * Allocates a ttm structure for the given BO.
 */
int ttm_tt_create(struct ttm_buffer_object *bo, bool zero_alloc)
{
	struct ttm_device *bdev = bo->bdev;
	uint32_t page_flags = 0;

	dma_resv_assert_held(bo->base.resv);

	if (bo->ttm)
		return 0;

	switch (bo->type) {
	case ttm_bo_type_device:
		if (zero_alloc)
			page_flags |= TTM_TT_FLAG_ZERO_ALLOC;
		break;
	case ttm_bo_type_kernel:
		break;
	case ttm_bo_type_sg:
		page_flags |= TTM_TT_FLAG_EXTERNAL;
		break;
	default:
		pr_err("Illegal buffer object type\n");
		return -EINVAL;
	}

	bo->ttm = bdev->funcs->ttm_tt_create(bo, page_flags);
	if (unlikely(bo->ttm == NULL))
		return -ENOMEM;

	WARN_ON(bo->ttm->page_flags & TTM_TT_FLAG_EXTERNAL_MAPPABLE &&
		!(bo->ttm->page_flags & TTM_TT_FLAG_EXTERNAL));

	return 0;
}

/*
 * Allocates storage for pointers to the pages that back the ttm.
 */
static int ttm_tt_alloc_page_directory(struct ttm_tt *ttm)
{
	ttm->pages = kvmalloc_array(ttm->num_pages, sizeof(void*),
			GFP_KERNEL | __GFP_ZERO);
	if (!ttm->pages)
		return -ENOMEM;
	return 0;
}

static int ttm_dma_tt_alloc_page_directory(struct ttm_tt *ttm)
{
	ttm->pages = kvmalloc_array(ttm->num_pages,
				    sizeof(*ttm->pages) +
				    sizeof(*ttm->dma_address),
				    GFP_KERNEL | __GFP_ZERO);
	if (!ttm->pages)
		return -ENOMEM;

	ttm->dma_address = (void *)(ttm->pages + ttm->num_pages);
	return 0;
}

static int ttm_sg_tt_alloc_page_directory(struct ttm_tt *ttm)
{
	ttm->dma_address = kvmalloc_array(ttm->num_pages,
					  sizeof(*ttm->dma_address),
					  GFP_KERNEL | __GFP_ZERO);
	if (!ttm->dma_address)
		return -ENOMEM;
	return 0;
}


void ttm_tt_destroy_common(struct ttm_device *bdev, struct ttm_tt *ttm)
{
	ttm_tt_unpopulate(bdev, ttm);

	if (ttm->swap_storage)
		fput(ttm->swap_storage);

	ttm->swap_storage = NULL;
}
EXPORT_SYMBOL(ttm_tt_destroy_common);

void ttm_tt_destroy(struct ttm_device *bdev, struct ttm_tt *ttm)
{
	bdev->funcs->ttm_tt_destroy(bdev, ttm);
}

static void ttm_tt_init_fields(struct ttm_tt *ttm,
			       struct ttm_buffer_object *bo,
			       uint32_t page_flags,
			       enum ttm_caching caching)
{
	ttm->num_pages = PAGE_ALIGN(bo->base.size) >> PAGE_SHIFT;
	ttm->caching = ttm_cached;
	ttm->page_flags = page_flags;
	ttm->dma_address = NULL;
	ttm->swap_storage = NULL;
	ttm->sg = bo->sg;
	ttm->caching = caching;
}

int ttm_tt_init(struct ttm_tt *ttm, struct ttm_buffer_object *bo,
		uint32_t page_flags, enum ttm_caching caching)
{
	ttm_tt_init_fields(ttm, bo, page_flags, caching);

	if (ttm_tt_alloc_page_directory(ttm)) {
		pr_err("Failed allocating page table\n");
		return -ENOMEM;
	}
	return 0;
}
EXPORT_SYMBOL(ttm_tt_init);

void ttm_tt_fini(struct ttm_tt *ttm)
{
	WARN_ON(ttm->page_flags & TTM_TT_FLAG_PRIV_POPULATED);

	if (ttm->swap_storage)
		fput(ttm->swap_storage);
	ttm->swap_storage = NULL;

	if (ttm->pages)
		kvfree(ttm->pages);
	else
		kvfree(ttm->dma_address);
	ttm->pages = NULL;
	ttm->dma_address = NULL;
}
EXPORT_SYMBOL(ttm_tt_fini);

int ttm_sg_tt_init(struct ttm_tt *ttm, struct ttm_buffer_object *bo,
		   uint32_t page_flags, enum ttm_caching caching)
{
	int ret;

	ttm_tt_init_fields(ttm, bo, page_flags, caching);

	if (page_flags & TTM_TT_FLAG_EXTERNAL)
		ret = ttm_sg_tt_alloc_page_directory(ttm);
	else
		ret = ttm_dma_tt_alloc_page_directory(ttm);
	if (ret) {
		pr_err("Failed allocating page table\n");
		return -ENOMEM;
	}
	return 0;
}
EXPORT_SYMBOL(ttm_sg_tt_init);

int ttm_tt_swapin(struct ttm_tt *ttm)
{
	struct address_space *swap_space;
	struct file *swap_storage;
	struct page *from_page;
	struct page *to_page;
	gfp_t gfp_mask;
	int i, ret;

	swap_storage = ttm->swap_storage;
	BUG_ON(swap_storage == NULL);

	swap_space = swap_storage->f_mapping;
	gfp_mask = mapping_gfp_mask(swap_space);

	for (i = 0; i < ttm->num_pages; ++i) {
		from_page = shmem_read_mapping_page_gfp(swap_space, i,
							gfp_mask);
		if (IS_ERR(from_page)) {
			ret = PTR_ERR(from_page);
			goto out_err;
		}
		to_page = ttm->pages[i];
		if (unlikely(to_page == NULL)) {
			ret = -ENOMEM;
			goto out_err;
		}

		copy_highpage(to_page, from_page);
		put_page(from_page);
	}

	fput(swap_storage);
	ttm->swap_storage = NULL;
	ttm->page_flags &= ~TTM_TT_FLAG_SWAPPED;

	return 0;

out_err:
	return ret;
}

/**
 * ttm_tt_swapout - swap out tt object
 *
 * @bdev: TTM device structure.
 * @ttm: The struct ttm_tt.
 * @gfp_flags: Flags to use for memory allocation.
 *
 * Swapout a TT object to a shmem_file, return number of pages swapped out or
 * negative error code.
 */
int ttm_tt_swapout(struct ttm_device *bdev, struct ttm_tt *ttm,
		   gfp_t gfp_flags)
{
	loff_t size = (loff_t)ttm->num_pages << PAGE_SHIFT;
	struct address_space *swap_space;
	struct file *swap_storage;
	struct page *from_page;
	struct page *to_page;
	int i, ret;

	swap_storage = shmem_file_setup("ttm swap", size, 0);
	if (IS_ERR(swap_storage)) {
		pr_err("Failed allocating swap storage\n");
		return PTR_ERR(swap_storage);
	}

	swap_space = swap_storage->f_mapping;
	gfp_flags &= mapping_gfp_mask(swap_space);

	for (i = 0; i < ttm->num_pages; ++i) {
		from_page = ttm->pages[i];
		if (unlikely(from_page == NULL))
			continue;

		to_page = shmem_read_mapping_page_gfp(swap_space, i, gfp_flags);
		if (IS_ERR(to_page)) {
			ret = PTR_ERR(to_page);
			goto out_err;
		}
		copy_highpage(to_page, from_page);
		set_page_dirty(to_page);
		mark_page_accessed(to_page);
		put_page(to_page);
	}

	ttm_tt_unpopulate(bdev, ttm);
	ttm->swap_storage = swap_storage;
	ttm->page_flags |= TTM_TT_FLAG_SWAPPED;

	return ttm->num_pages;

out_err:
	fput(swap_storage);

	return ret;
}


int ttm_tt_populate(struct ttm_device *bdev,
		    struct ttm_tt *ttm, struct ttm_operation_ctx *ctx)
{
	int ret;

	if (!ttm)
		return -EINVAL;

	if (ttm_tt_is_populated(ttm))
		return 0;

	if (!(ttm->page_flags & TTM_TT_FLAG_EXTERNAL)) {
		atomic_long_add(ttm->num_pages, &ttm_pages_allocated);
		if (bdev->pool.use_dma32)
			atomic_long_add(ttm->num_pages,
					&ttm_dma32_pages_allocated);
	}

	while (atomic_long_read(&ttm_pages_allocated) > ttm_pages_limit ||
	       atomic_long_read(&ttm_dma32_pages_allocated) >
	       ttm_dma32_pages_limit) {

		ret = ttm_global_swapout(ctx, GFP_KERNEL);
		if (ret == 0)
			break;
		if (ret < 0)
			goto error;
	}

	if (bdev->funcs->ttm_tt_populate)
		ret = bdev->funcs->ttm_tt_populate(bdev, ttm, ctx);
	else
		ret = ttm_pool_alloc(&bdev->pool, ttm, ctx);
	if (ret)
		goto error;

	ttm->page_flags |= TTM_TT_FLAG_PRIV_POPULATED;
	if (unlikely(ttm->page_flags & TTM_TT_FLAG_SWAPPED)) {
		ret = ttm_tt_swapin(ttm);
		if (unlikely(ret != 0)) {
			ttm_tt_unpopulate(bdev, ttm);
			return ret;
		}
	}

	return 0;

error:
	if (!(ttm->page_flags & TTM_TT_FLAG_EXTERNAL)) {
		atomic_long_sub(ttm->num_pages, &ttm_pages_allocated);
		if (bdev->pool.use_dma32)
			atomic_long_sub(ttm->num_pages,
					&ttm_dma32_pages_allocated);
	}
	return ret;
}
EXPORT_SYMBOL(ttm_tt_populate);

void ttm_tt_unpopulate(struct ttm_device *bdev, struct ttm_tt *ttm)
{
	if (!ttm_tt_is_populated(ttm))
		return;

	if (bdev->funcs->ttm_tt_unpopulate)
		bdev->funcs->ttm_tt_unpopulate(bdev, ttm);
	else
		ttm_pool_free(&bdev->pool, ttm);

	if (!(ttm->page_flags & TTM_TT_FLAG_EXTERNAL)) {
		atomic_long_sub(ttm->num_pages, &ttm_pages_allocated);
		if (bdev->pool.use_dma32)
			atomic_long_sub(ttm->num_pages,
					&ttm_dma32_pages_allocated);
	}

	ttm->page_flags &= ~TTM_TT_FLAG_PRIV_POPULATED;
}

#ifdef CONFIG_DEBUG_FS

/* Test the shrinker functions and dump the result */
static int ttm_tt_debugfs_shrink_show(struct seq_file *m, void *data)
{
	struct ttm_operation_ctx ctx = { false, false };

	seq_printf(m, "%d\n", ttm_global_swapout(&ctx, GFP_KERNEL));
	return 0;
}
DEFINE_SHOW_ATTRIBUTE(ttm_tt_debugfs_shrink);

#endif


/*
 * ttm_tt_mgr_init - register with the MM shrinker
 *
 * Register with the MM shrinker for swapping out BOs.
 */
void ttm_tt_mgr_init(unsigned long num_pages, unsigned long num_dma32_pages)
{
#ifdef CONFIG_DEBUG_FS
	debugfs_create_file("tt_shrink", 0400, ttm_debugfs_root, NULL,
			    &ttm_tt_debugfs_shrink_fops);
#endif

	if (!ttm_pages_limit)
		ttm_pages_limit = num_pages;

	if (!ttm_dma32_pages_limit)
		ttm_dma32_pages_limit = num_dma32_pages;
}

static void ttm_kmap_iter_tt_map_local(struct ttm_kmap_iter *iter,
				       struct dma_buf_map *dmap,
				       pgoff_t i)
{
	struct ttm_kmap_iter_tt *iter_tt =
		container_of(iter, typeof(*iter_tt), base);

	dma_buf_map_set_vaddr(dmap, kmap_local_page_prot(iter_tt->tt->pages[i],
							 iter_tt->prot));
}

static void ttm_kmap_iter_tt_unmap_local(struct ttm_kmap_iter *iter,
					 struct dma_buf_map *map)
{
	kunmap_local(map->vaddr);
}

static const struct ttm_kmap_iter_ops ttm_kmap_iter_tt_ops = {
	.map_local = ttm_kmap_iter_tt_map_local,
	.unmap_local = ttm_kmap_iter_tt_unmap_local,
	.maps_tt = true,
};

/**
 * ttm_kmap_iter_tt_init - Initialize a struct ttm_kmap_iter_tt
 * @iter_tt: The struct ttm_kmap_iter_tt to initialize.
 * @tt: Struct ttm_tt holding page pointers of the struct ttm_resource.
 *
 * Return: Pointer to the embedded struct ttm_kmap_iter.
 */
struct ttm_kmap_iter *
ttm_kmap_iter_tt_init(struct ttm_kmap_iter_tt *iter_tt,
		      struct ttm_tt *tt)
{
	iter_tt->base.ops = &ttm_kmap_iter_tt_ops;
	iter_tt->tt = tt;
	if (tt)
		iter_tt->prot = ttm_prot_from_caching(tt->caching, PAGE_KERNEL);
	else
		iter_tt->prot = PAGE_KERNEL;

	return &iter_tt->base;
}
EXPORT_SYMBOL(ttm_kmap_iter_tt_init);
```

full `include/drm/ttm/ttm_tt.h`(tested in 5.15 & 5.16)

```c
/**************************************************************************
 *
 * Copyright (c) 2006-2009 Vmware, Inc., Palo Alto, CA., USA
 * All Rights Reserved.
 *
 * Permission is hereby granted, free of charge, to any person obtaining a
 * copy of this software and associated documentation files (the
 * "Software"), to deal in the Software without restriction, including
 * without limitation the rights to use, copy, modify, merge, publish,
 * distribute, sub license, and/or sell copies of the Software, and to
 * permit persons to whom the Software is furnished to do so, subject to
 * the following conditions:
 *
 * The above copyright notice and this permission notice (including the
 * next paragraph) shall be included in all copies or substantial portions
 * of the Software.
 *
 * THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
 * IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
 * FITNESS FOR A PARTICULAR PURPOSE AND NON-INFRINGEMENT. IN NO EVENT SHALL
 * THE COPYRIGHT HOLDERS, AUTHORS AND/OR ITS SUPPLIERS BE LIABLE FOR ANY CLAIM,
 * DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR
 * OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE
 * USE OR OTHER DEALINGS IN THE SOFTWARE.
 *
 **************************************************************************/
#ifndef _TTM_TT_H_
#define _TTM_TT_H_

#include <linux/pagemap.h>
#include <linux/types.h>
#include <drm/ttm/ttm_caching.h>
#include <drm/ttm/ttm_kmap_iter.h>

struct ttm_device;
struct ttm_tt;
struct ttm_resource;
struct ttm_buffer_object;
struct ttm_operation_ctx;

/**
 * struct ttm_tt - This is a structure holding the pages, caching- and aperture
 * binding status for a buffer object that isn't backed by fixed (VRAM / AGP)
 * memory.
 */
struct ttm_tt {
	/** @pages: Array of pages backing the data. */
	struct page **pages;
	/**
	 * @page_flags: The page flags.
	 *
	 * Supported values:
	 *
	 * TTM_TT_FLAG_SWAPPED: Set by TTM when the pages have been unpopulated
	 * and swapped out by TTM.  Calling ttm_tt_populate() will then swap the
	 * pages back in, and unset the flag. Drivers should in general never
	 * need to touch this.
	 *
	 * TTM_TT_FLAG_ZERO_ALLOC: Set if the pages will be zeroed on
	 * allocation.
	 *
	 * TTM_TT_FLAG_EXTERNAL: Set if the underlying pages were allocated
	 * externally, like with dma-buf or userptr. This effectively disables
	 * TTM swapping out such pages.  Also important is to prevent TTM from
	 * ever directly mapping these pages.
	 *
	 * Note that enum ttm_bo_type.ttm_bo_type_sg objects will always enable
	 * this flag.
	 *
	 * TTM_TT_FLAG_EXTERNAL_MAPPABLE: Same behaviour as
	 * TTM_TT_FLAG_EXTERNAL, but with the reduced restriction that it is
	 * still valid to use TTM to map the pages directly. This is useful when
	 * implementing a ttm_tt backend which still allocates driver owned
	 * pages underneath(say with shmem).
	 *
	 * Note that since this also implies TTM_TT_FLAG_EXTERNAL, the usage
	 * here should always be:
	 *
	 *   page_flags = TTM_TT_FLAG_EXTERNAL |
	 *		  TTM_TT_FLAG_EXTERNAL_MAPPABLE;
	 *
	 * TTM_TT_FLAG_PRIV_POPULATED: TTM internal only. DO NOT USE. This is
	 * set by TTM after ttm_tt_populate() has successfully returned, and is
	 * then unset when TTM calls ttm_tt_unpopulate().
	 */
#define TTM_TT_FLAG_SWAPPED		(1 << 0)
#define TTM_TT_FLAG_ZERO_ALLOC		(1 << 1)
#define TTM_TT_FLAG_EXTERNAL		(1 << 2)
#define TTM_TT_FLAG_EXTERNAL_MAPPABLE	(1 << 3)
#define TTM_PAGE_FLAG_SG              (1 << 8)

#define TTM_TT_FLAG_PRIV_POPULATED  (1 << 31)
	uint32_t page_flags;
	/** @num_pages: Number of pages in the page array. */
	uint32_t num_pages;
	/** @sg: for SG objects via dma-buf. */
	struct sg_table *sg;
	/** @dma_address: The DMA (bus) addresses of the pages. */
	dma_addr_t *dma_address;
	/** @swap_storage: Pointer to shmem struct file for swap storage. */
	struct file *swap_storage;
	/**
	 * @caching: The current caching state of the pages, see enum
	 * ttm_caching.
	 */
	enum ttm_caching caching;
};

/**
 * struct ttm_kmap_iter_tt - Specialization of a mappig iterator for a tt.
 * @base: Embedded struct ttm_kmap_iter providing the usage interface
 * @tt: Cached struct ttm_tt.
 * @prot: Cached page protection for mapping.
 */
struct ttm_kmap_iter_tt {
	struct ttm_kmap_iter base;
	struct ttm_tt *tt;
	pgprot_t prot;
};

static inline bool ttm_tt_is_populated(struct ttm_tt *tt)
{
	return tt->page_flags & TTM_TT_FLAG_PRIV_POPULATED;
}

/**
 * ttm_tt_create
 *
 * @bo: pointer to a struct ttm_buffer_object
 * @zero_alloc: true if allocated pages needs to be zeroed
 *
 * Make sure we have a TTM structure allocated for the given BO.
 * No pages are actually allocated.
 */
int ttm_tt_create(struct ttm_buffer_object *bo, bool zero_alloc);

/**
 * ttm_tt_init
 *
 * @ttm: The struct ttm_tt.
 * @bo: The buffer object we create the ttm for.
 * @page_flags: Page flags as identified by TTM_TT_FLAG_XX flags.
 * @caching: the desired caching state of the pages
 *
 * Create a struct ttm_tt to back data with system memory pages.
 * No pages are actually allocated.
 * Returns:
 * NULL: Out of memory.
 */
int ttm_tt_init(struct ttm_tt *ttm, struct ttm_buffer_object *bo,
		uint32_t page_flags, enum ttm_caching caching);
int ttm_sg_tt_init(struct ttm_tt *ttm_dma, struct ttm_buffer_object *bo,
		   uint32_t page_flags, enum ttm_caching caching);

/**
 * ttm_tt_fini
 *
 * @ttm: the ttm_tt structure.
 *
 * Free memory of ttm_tt structure
 */
void ttm_tt_fini(struct ttm_tt *ttm);

/**
 * ttm_tt_destroy:
 *
 * @bdev: the ttm_device this object belongs to
 * @ttm: The struct ttm_tt.
 *
 * Unbind, unpopulate and destroy common struct ttm_tt.
 */
void ttm_tt_destroy(struct ttm_device *bdev, struct ttm_tt *ttm);

/**
 * ttm_tt_destroy_common:
 *
 * Called from driver to destroy common path.
 */
void ttm_tt_destroy_common(struct ttm_device *bdev, struct ttm_tt *ttm);


/**
 * ttm_tt_swapin:
 *
 * @ttm: The struct ttm_tt.
 *
 * Swap in a previously swap out ttm_tt.
 */
int ttm_tt_swapin(struct ttm_tt *ttm);
int ttm_tt_swapout(struct ttm_device *bdev, struct ttm_tt *ttm,
		   gfp_t gfp_flags);

/**
 * ttm_tt_populate - allocate pages for a ttm
 *
 * @bdev: the ttm_device this object belongs to
 * @ttm: Pointer to the ttm_tt structure
 * @ctx: operation context for populating the tt object.
 *
 * Calls the driver method to allocate pages for a ttm
 */
int ttm_tt_populate(struct ttm_device *bdev, struct ttm_tt *ttm,
		    struct ttm_operation_ctx *ctx);

/**
 * ttm_tt_unpopulate - free pages from a ttm
 *
 * @bdev: the ttm_device this object belongs to
 * @ttm: Pointer to the ttm_tt structure
 *
 * Calls the driver method to free all pages from a ttm
 */
void ttm_tt_unpopulate(struct ttm_device *bdev, struct ttm_tt *ttm);

/**
 * ttm_tt_mark_for_clear - Mark pages for clearing on populate.
 *
 * @ttm: Pointer to the ttm_tt structure
 *
 * Marks pages for clearing so that the next time the page vector is
 * populated, the pages will be cleared.
 */
static inline void ttm_tt_mark_for_clear(struct ttm_tt *ttm)
{
	ttm->page_flags |= TTM_TT_FLAG_ZERO_ALLOC;
}

void ttm_tt_mgr_init(unsigned long num_pages, unsigned long num_dma32_pages);

struct ttm_kmap_iter *ttm_kmap_iter_tt_init(struct ttm_kmap_iter_tt *iter_tt,
					    struct ttm_tt *tt);

#if IS_ENABLED(CONFIG_AGP)
#include <linux/agp_backend.h>

/**
 * ttm_agp_tt_create
 *
 * @bo: Buffer object we allocate the ttm for.
 * @bridge: The agp bridge this device is sitting on.
 * @page_flags: Page flags as identified by TTM_TT_FLAG_XX flags.
 *
 *
 * Create a TTM backend that uses the indicated AGP bridge as an aperture
 * for TT memory. This function uses the linux agpgart interface to
 * bind and unbind memory backing a ttm_tt.
 */
struct ttm_tt *ttm_agp_tt_create(struct ttm_buffer_object *bo,
				 struct agp_bridge_data *bridge,
				 uint32_t page_flags);
int ttm_agp_bind(struct ttm_tt *ttm, struct ttm_resource *bo_mem);
void ttm_agp_unbind(struct ttm_tt *ttm);
void ttm_agp_destroy(struct ttm_tt *ttm);
bool ttm_agp_is_bound(struct ttm_tt *ttm);
#endif

#endif
```
