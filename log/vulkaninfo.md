`vulkaninfo`
```
'DISPLAY' environment variable not set... skipping surface info
WARNING: lavapipe is not a conformant vulkan implementation, testing use only.
==========
VULKANINFO
==========

Vulkan Instance Version: 1.2.162


Instance Extensions: count = 18
===============================
	VK_EXT_acquire_xlib_display            : extension revision 1
	VK_EXT_debug_report                    : extension revision 9
	VK_EXT_debug_utils                     : extension revision 2
	VK_EXT_direct_mode_display             : extension revision 1
	VK_EXT_display_surface_counter         : extension revision 1
	VK_KHR_device_group_creation           : extension revision 1
	VK_KHR_display                         : extension revision 23
	VK_KHR_external_fence_capabilities     : extension revision 1
	VK_KHR_external_memory_capabilities    : extension revision 1
	VK_KHR_external_semaphore_capabilities : extension revision 1
	VK_KHR_get_display_properties2         : extension revision 1
	VK_KHR_get_physical_device_properties2 : extension revision 1
	VK_KHR_get_surface_capabilities2       : extension revision 1
	VK_KHR_surface                         : extension revision 25
	VK_KHR_surface_protected_capabilities  : extension revision 1
	VK_KHR_wayland_surface                 : extension revision 6
	VK_KHR_xcb_surface                     : extension revision 6
	VK_KHR_xlib_surface                    : extension revision 6

Layers: count = 2
=================
VK_LAYER_MESA_device_select (Linux device selection layer) Vulkan version 1.2.73, layer version 1:
	Layer Extensions: count = 0
	Devices: count = 1
		GPU id = 0 (llvmpipe (LLVM 11.0.1, 128 bits))
		Layer-Device Extensions: count = 0

VK_LAYER_MESA_overlay (Mesa Overlay layer) Vulkan version 1.1.73, layer version 1:
	Layer Extensions: count = 0
	Devices: count = 1
		GPU id = 0 (llvmpipe (LLVM 11.0.1, 128 bits))
		Layer-Device Extensions: count = 0

Presentable Surfaces:
=====================

Device Groups:
==============
Group 0:
	Properties:
		physicalDevices: count = 1
			llvmpipe (LLVM 11.0.1, 128 bits) (ID: 0)
		subsetAllocation = 0

	Present Capabilities = Group does not support VK_KHR_device_group, skipping printing present capabilities


Device Properties and Extensions:
=================================
GPU0:
VkPhysicalDeviceProperties:
---------------------------
	apiVersion     = 4194306 (1.0.2)
	driverVersion  = 1 (0x0001)
	vendorID       = 0x10005
	deviceID       = 0x0000
	deviceType     = PHYSICAL_DEVICE_TYPE_CPU
	deviceName     = llvmpipe (LLVM 11.0.1, 128 bits)

VkPhysicalDeviceLimits:
-----------------------
	maxImageDimension1D                             = 16384
	maxImageDimension2D                             = 16384
	maxImageDimension3D                             = 4096
	maxImageDimensionCube                           = 32768
	maxImageArrayLayers                             = 2048
	maxTexelBufferElements                          = 134217728
	maxUniformBufferRange                           = 65536
	maxStorageBufferRange                           = 134217728
	maxPushConstantsSize                            = 128
	maxMemoryAllocationCount                        = 4096
	maxSamplerAllocationCount                       = 32768
	bufferImageGranularity                          = 0x00000040
	sparseAddressSpaceSize                          = 0x00000000
	maxBoundDescriptorSets                          = 8
	maxPerStageDescriptorSamplers                   = 32
	maxPerStageDescriptorUniformBuffers             = 16
	maxPerStageDescriptorStorageBuffers             = 16
	maxPerStageDescriptorSampledImages              = 128
	maxPerStageDescriptorStorageImages              = 128
	maxPerStageDescriptorInputAttachments           = 8
	maxPerStageResources                            = 128
	maxDescriptorSetSamplers                        = 32768
	maxDescriptorSetUniformBuffers                  = 256
	maxDescriptorSetUniformBuffersDynamic           = 256
	maxDescriptorSetStorageBuffers                  = 256
	maxDescriptorSetStorageBuffersDynamic           = 256
	maxDescriptorSetSampledImages                   = 256
	maxDescriptorSetStorageImages                   = 256
	maxDescriptorSetInputAttachments                = 256
	maxVertexInputAttributes                        = 32
	maxVertexInputBindings                          = 32
	maxVertexInputAttributeOffset                   = 2047
	maxVertexInputBindingStride                     = 2048
	maxVertexOutputComponents                       = 128
	maxTessellationGenerationLevel                  = 64
	maxTessellationPatchSize                        = 32
	maxTessellationControlPerVertexInputComponents  = 128
	maxTessellationControlPerVertexOutputComponents = 128
	maxTessellationControlPerPatchOutputComponents  = 128
	maxTessellationControlTotalOutputComponents     = 4096
	maxTessellationEvaluationInputComponents        = 128
	maxTessellationEvaluationOutputComponents       = 128
	maxGeometryShaderInvocations                    = 32
	maxGeometryInputComponents                      = 64
	maxGeometryOutputComponents                     = 128
	maxGeometryOutputVertices                       = 1024
	maxGeometryTotalOutputComponents                = 1024
	maxFragmentInputComponents                      = 128
	maxFragmentOutputAttachments                    = 8
	maxFragmentDualSrcAttachments                   = 2
	maxFragmentCombinedOutputResources              = 8
	maxComputeSharedMemorySize                      = 32768
	maxComputeWorkGroupCount: count = 3
		65535
		65535
		65535
	maxComputeWorkGroupInvocations                  = 1024
	maxComputeWorkGroupSize: count = 3
		1024
		1024
		1024
	subPixelPrecisionBits                           = 8
	subTexelPrecisionBits                           = 8
	mipmapPrecisionBits                             = 8
	maxDrawIndexedIndexValue                        = 4294967295
	maxDrawIndirectCount                            = 4294967295
	maxSamplerLodBias                               = 16
	maxSamplerAnisotropy                            = 16
	maxViewports                                    = 16
	maxViewportDimensions: count = 2
		16384
		16384
	viewportBoundsRange: count = 2
		-32768
		32768
	viewportSubPixelBits                            = 0
	minMemoryMapAlignment                           = 4096
	minTexelBufferOffsetAlignment                   = 0x00000010
	minUniformBufferOffsetAlignment                 = 0x00000010
	minStorageBufferOffsetAlignment                 = 0x00000010
	minTexelOffset                                  = -32
	maxTexelOffset                                  = 31
	minTexelGatherOffset                            = -32
	maxTexelGatherOffset                            = 31
	minInterpolationOffset                          = -2
	maxInterpolationOffset                          = 2
	subPixelInterpolationOffsetBits                 = 8
	maxFramebufferWidth                             = 16384
	maxFramebufferHeight                            = 16384
	maxFramebufferLayers                            = 2048
	framebufferColorSampleCounts: count = 2
		SAMPLE_COUNT_1_BIT
		SAMPLE_COUNT_4_BIT
	framebufferDepthSampleCounts: count = 2
		SAMPLE_COUNT_1_BIT
		SAMPLE_COUNT_4_BIT
	framebufferStencilSampleCounts: count = 2
		SAMPLE_COUNT_1_BIT
		SAMPLE_COUNT_4_BIT
	framebufferNoAttachmentsSampleCounts: count = 2
		SAMPLE_COUNT_1_BIT
		SAMPLE_COUNT_4_BIT
	maxColorAttachments                             = 8
	sampledImageColorSampleCounts: count = 2
		SAMPLE_COUNT_1_BIT
		SAMPLE_COUNT_4_BIT
	sampledImageIntegerSampleCounts: count = 2
		SAMPLE_COUNT_1_BIT
		SAMPLE_COUNT_4_BIT
	sampledImageDepthSampleCounts: count = 2
		SAMPLE_COUNT_1_BIT
		SAMPLE_COUNT_4_BIT
	sampledImageStencilSampleCounts: count = 2
		SAMPLE_COUNT_1_BIT
		SAMPLE_COUNT_4_BIT
	storageImageSampleCounts: count = 2
		SAMPLE_COUNT_1_BIT
		SAMPLE_COUNT_4_BIT
	maxSampleMaskWords                              = 1
	timestampComputeAndGraphics                     = true
	timestampPeriod                                 = 1
	maxClipDistances                                = 8
	maxCullDistances                                = 8
	maxCombinedClipAndCullDistances                 = 8
	discreteQueuePriorities                         = 2
	pointSizeRange: count = 2
		0
		255
	lineWidthRange: count = 2
		1
		1
	pointSizeGranularity                            = 0.125
	lineWidthGranularity                            = 0
	strictLines                                     = false
	standardSampleLocations                         = true
	optimalBufferCopyOffsetAlignment                = 0x00000080
	optimalBufferCopyRowPitchAlignment              = 0x00000080
	nonCoherentAtomSize                             = 0x00000040

VkPhysicalDeviceSparseProperties:
---------------------------------
	residencyStandard2DBlockShape            = false
	residencyStandard2DMultisampleBlockShape = false
	residencyStandard3DBlockShape            = false
	residencyAlignedMipSize                  = false
	residencyNonResidentStrict               = false

VkPhysicalDeviceDriverPropertiesKHR:
------------------------------------
	driverID           = DRIVER_ID_MESA_LLVMPIPE
	driverName         = llvmpipe
	driverInfo         = Mesa 20.3.5 (LLVM 11.0.1)
	conformanceVersion = 1.0.0.0

VkPhysicalDeviceIDPropertiesKHR:
--------------------------------
	deviceUUID      = 00000000-0000-0000-0000-000000000000
	driverUUID      = 00000000-0000-0000-0000-000000000000
	deviceNodeMask  = 0
	deviceLUIDValid = false


Device Extensions: count = 14
-----------------------------
	VK_EXT_external_memory_dma_buf      : extension revision 1
	VK_EXT_private_data                 : extension revision 1
	VK_GOOGLE_decorate_string           : extension revision 1
	VK_GOOGLE_hlsl_functionality1       : extension revision 1
	VK_KHR_bind_memory2                 : extension revision 1
	VK_KHR_dedicated_allocation         : extension revision 1
	VK_KHR_driver_properties            : extension revision 1
	VK_KHR_get_memory_requirements2     : extension revision 1
	VK_KHR_incremental_present          : extension revision 1
	VK_KHR_maintenance1                 : extension revision 1
	VK_KHR_relaxed_block_layout         : extension revision 1
	VK_KHR_sampler_mirror_clamp_to_edge : extension revision 1
	VK_KHR_storage_buffer_storage_class : extension revision 1
	VK_KHR_swapchain                    : extension revision 68

VkQueueFamilyProperties:
========================
	queueProperties[0]:
	-------------------
		minImageTransferGranularity = (1,1,1)
		queueCount                  = 1
		queueFlags                  = QUEUE_GRAPHICS | QUEUE_COMPUTE | QUEUE_TRANSFER
		timestampValidBits          = 64
		present support             = false

VkPhysicalDeviceMemoryProperties:
=================================
memoryHeaps: count = 1
	memoryHeaps[0]:
		size   = 2147483648 (0x80000000) (2.00 GiB)
		budget = 0 (0x00000000) (0.00 B)
		usage  = 0 (0x00000000) (0.00 B)
		flags: count = 1
			MEMORY_HEAP_DEVICE_LOCAL_BIT
memoryTypes: count = 1
	memoryTypes[0]:
		heapIndex     = 0
		propertyFlags = 0x000f: count = 4
			MEMORY_PROPERTY_DEVICE_LOCAL_BIT
			MEMORY_PROPERTY_HOST_VISIBLE_BIT
			MEMORY_PROPERTY_HOST_COHERENT_BIT
			MEMORY_PROPERTY_HOST_CACHED_BIT
		usable for:
			IMAGE_TILING_OPTIMAL:
				color images
				FORMAT_D16_UNORM
				FORMAT_X8_D24_UNORM_PACK32
				FORMAT_D32_SFLOAT
				FORMAT_S8_UINT
				FORMAT_D24_UNORM_S8_UINT
				FORMAT_D32_SFLOAT_S8_UINT
				(non-sparse)
			IMAGE_TILING_LINEAR:
				color images
				(non-sparse)

VkPhysicalDeviceFeatures:
=========================
	robustBufferAccess                      = true
	fullDrawIndexUint32                     = true
	imageCubeArray                          = true
	independentBlend                        = true
	geometryShader                          = true
	tessellationShader                      = true
	sampleRateShading                       = true
	dualSrcBlend                            = true
	logicOp                                 = true
	multiDrawIndirect                       = true
	drawIndirectFirstInstance               = true
	depthClamp                              = true
	depthBiasClamp                          = true
	fillModeNonSolid                        = true
	depthBounds                             = false
	wideLines                               = false
	largePoints                             = true
	alphaToOne                              = true
	multiViewport                           = true
	samplerAnisotropy                       = false
	textureCompressionETC2                  = false
	textureCompressionASTC_LDR              = false
	textureCompressionBC                    = true
	occlusionQueryPrecise                   = true
	pipelineStatisticsQuery                 = true
	vertexPipelineStoresAndAtomics          = true
	fragmentStoresAndAtomics                = true
	shaderTessellationAndGeometryPointSize  = true
	shaderImageGatherExtended               = true
	shaderStorageImageExtendedFormats       = false
	shaderStorageImageMultisample           = true
	shaderStorageImageReadWithoutFormat     = false
	shaderStorageImageWriteWithoutFormat    = true
	shaderUniformBufferArrayDynamicIndexing = false
	shaderSampledImageArrayDynamicIndexing  = false
	shaderStorageBufferArrayDynamicIndexing = false
	shaderStorageImageArrayDynamicIndexing  = false
	shaderClipDistance                      = true
	shaderCullDistance                      = true
	shaderFloat64                           = true
	shaderInt64                             = true
	shaderInt16                             = true
	shaderResourceResidency                 = false
	shaderResourceMinLod                    = false
	sparseBinding                           = false
	sparseResidencyBuffer                   = false
	sparseResidencyImage2D                  = false
	sparseResidencyImage3D                  = false
	sparseResidency2Samples                 = false
	sparseResidency4Samples                 = false
	sparseResidency8Samples                 = false
	sparseResidency16Samples                = false
	sparseResidencyAliased                  = false
	variableMultisampleRate                 = false
	inheritedQueries                        = false

VkPhysicalDevicePrivateDataFeaturesEXT:
---------------------------------------
	privateData = true
```
