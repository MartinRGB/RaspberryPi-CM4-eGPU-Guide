`glmark2-drm`
```
=======================================================
    glmark2 2023.01
=======================================================
    OpenGL Information
    GL_VENDOR:      X.Org
    GL_RENDERER:    AMD BARTS (DRM 2.50.0 / 5.10.17-v8+, LLVM 11.0.1)
    GL_VERSION:     3.1 Mesa 20.3.5
    Surface Config: buf=32 r=8 g=8 b=8 a=8 depth=24 stencil=0 samples=0
    Surface Size:   1920x1080 fullscreen
=======================================================
[build] use-vbo=false: FPS: 65 FrameTime: 15.397 ms
[build] use-vbo=true: FPS: 427 FrameTime: 2.346 ms
[texture] texture-filter=nearest: FPS: 425 FrameTime: 2.358 ms
[texture] texture-filter=linear: FPS: 424 FrameTime: 2.360 ms
[texture] texture-filter=mipmap: FPS: 422 FrameTime: 2.371 ms
[shading] shading=gouraud: FPS: 423 FrameTime: 2.366 ms
[shading] shading=blinn-phong-inf: FPS: 424 FrameTime: 2.364 ms
[shading] shading=phong: FPS: 415 FrameTime: 2.414 ms
[shading] shading=cel: FPS: 413 FrameTime: 2.424 ms
[bump] bump-render=high-poly: FPS: 417 FrameTime: 2.401 ms
[bump] bump-render=normals: FPS: 418 FrameTime: 2.396 ms
[bump] bump-render=height: FPS: 419 FrameTime: 2.387 ms
[effect2d] kernel=0,1,0;1,-4,1;0,1,0;: FPS: 427 FrameTime: 2.343 ms
[effect2d] kernel=1,1,1,1,1;1,1,1,1,1;1,1,1,1,1;: FPS: 445 FrameTime: 2.248 ms
[pulsar] light=false:quads=5:texture=false: FPS: 402 FrameTime: 2.488 ms
[desktop] blur-radius=5:effect=blur:passes=1:separable=true:windows=4: FPS: 318 FrameTime: 3.154 ms
[desktop] effect=shadow:windows=4: FPS: 323 FrameTime: 3.102 ms
[buffer] columns=200:interleave=false:update-dispersion=0.9:update-fraction=0.5:update-method=map: FPS: 11 FrameTime: 96.370 ms
[buffer] columns=200:interleave=false:update-dispersion=0.9:update-fraction=0.5:update-method=subdata: FPS: 30 FrameTime: 33.606 ms
[buffer] columns=200:interleave=true:update-dispersion=0.9:update-fraction=0.5:update-method=map: FPS: 11 FrameTime: 91.517 ms
[ideas] speed=duration: FPS: 202 FrameTime: 4.953 ms
[jellyfish] <default>: FPS: 382 FrameTime: 2.622 ms
[terrain] <default>: FPS: 138 FrameTime: 7.293 ms
[shadow] <default>: FPS: 367 FrameTime: 2.732 ms
[refract] <default>: FPS: 307 FrameTime: 3.265 ms
[conditionals] fragment-steps=0:vertex-steps=0: FPS: 414 FrameTime: 2.416 ms
[conditionals] fragment-steps=5:vertex-steps=0: FPS: 414 FrameTime: 2.417 ms
[conditionals] fragment-steps=0:vertex-steps=5: FPS: 413 FrameTime: 2.424 ms
[function] fragment-complexity=low:fragment-steps=5: FPS: 414 FrameTime: 2.417 ms
[function] fragment-complexity=medium:fragment-steps=5: FPS: 412 FrameTime: 2.433 ms
[loop] fragment-loop=false:fragment-steps=5:vertex-steps=5: FPS: 415 FrameTime: 2.414 ms
[loop] fragment-steps=5:fragment-uniform=false:vertex-steps=5: FPS: 415 FrameTime: 2.413 ms
[loop] fragment-steps=5:fragment-uniform=true:vertex-steps=5: FPS: 412 FrameTime: 2.428 ms
=======================================================
                                  glmark2 Score: 343
=======================================================
```

`with modified mesa version `

```
=======================================================
    glmark2 2023.01
=======================================================
    OpenGL Information
    GL_VENDOR:      X.Org
    GL_RENDERER:    AMD BARTS (DRM 2.50.0 / 6.5.0-v8+, LLVM 11.0.1)
    GL_VERSION:     3.1 Mesa 21.3.0-devel (git-8d4cebeb3f)
    Surface Config: buf=32 r=8 g=8 b=8 a=8 depth=24 stencil=0 samples=0
    Surface Size:   1920x1200 fullscreen
=======================================================
[build] use-vbo=false: FPS: 80 FrameTime: 12.643 ms
[build] use-vbo=true: FPS: 4001 FrameTime: 0.250 ms
[texture] texture-filter=nearest: FPS: 3820 FrameTime: 0.262 ms
[texture] texture-filter=linear: FPS: 3781 FrameTime: 0.265 ms
[texture] texture-filter=mipmap: FPS: 3829 FrameTime: 0.261 ms
[shading] shading=gouraud: FPS: 3851 FrameTime: 0.260 ms
[shading] shading=blinn-phong-inf: FPS: 3822 FrameTime: 0.262 ms
[shading] shading=phong: FPS: 3613 FrameTime: 0.277 ms
[shading] shading=cel: FPS: 3527 FrameTime: 0.284 ms
[bump] bump-render=high-poly: FPS: 3165 FrameTime: 0.316 ms
[bump] bump-render=normals: FPS: 3825 FrameTime: 0.261 ms
[bump] bump-render=height: FPS: 3786 FrameTime: 0.264 ms
[effect2d] kernel=0,1,0;1,-4,1;0,1,0;: FPS: 2896 FrameTime: 0.345 ms
[effect2d] kernel=1,1,1,1,1;1,1,1,1,1;1,1,1,1,1;: FPS: 1249 FrameTime: 0.801 ms
[pulsar] light=false:quads=5:texture=false: FPS: 2759 FrameTime: 0.362 ms
[desktop] blur-radius=5:effect=blur:passes=1:separable=true:windows=4: FPS: 1031 FrameTime: 0.970 ms
[desktop] effect=shadow:windows=4: FPS: 1249 FrameTime: 0.801 ms
[buffer] columns=200:interleave=false:update-dispersion=0.9:update-fraction=0.5:update-method=map:User mapped Buffer with flags GL_MAP_WRITE
flushed and unmapped shadow buffer
User mapped Buffer with flags GL_MAP_WRITE
...
User mapped Buffer with flags GL_MAP_WRITE
flushed and unmapped shadow buffer
 FPS: 5 FrameTime: 215.305 ms
[buffer] columns=200:interleave=false:update-dispersion=0.9:update-fraction=0.5:update-method=subdata: FPS: 12 FrameTime: 90.031 ms
[buffer] columns=200:interleave=true:update-dispersion=0.9:update-fraction=0.5:update-method=map:User mapped Buffer with flags GL_MAP_WRITE
flushed and unmapped shadow buffer
...
User mapped Buffer with flags GL_MAP_WRITE
flushed and unmapped shadow buffer
 FPS: 7 FrameTime: 165.517 ms
[ideas] speed=duration: FPS: 307 FrameTime: 3.266 ms
[jellyfish] <default>: FPS: 2281 FrameTime: 0.439 ms
[terrain] <default>: FPS: 248 FrameTime: 4.036 ms
[shadow] <default>: FPS: 1141 FrameTime: 0.877 ms
[refract] <default>: FPS: 371 FrameTime: 2.700 ms
[conditionals] fragment-steps=0:vertex-steps=0: FPS: 3395 FrameTime: 0.295 ms
[conditionals] fragment-steps=5:vertex-steps=0: FPS: 3448 FrameTime: 0.290 ms
[conditionals] fragment-steps=0:vertex-steps=5: FPS: 3430 FrameTime: 0.292 ms
[function] fragment-complexity=low:fragment-steps=5: FPS: 3461 FrameTime: 0.289 ms
[function] fragment-complexity=medium:fragment-steps=5: FPS: 3249 FrameTime: 0.308 ms
[loop] fragment-loop=false:fragment-steps=5:vertex-steps=5: FPS: 3427 FrameTime: 0.292 ms
[loop] fragment-steps=5:fragment-uniform=false:vertex-steps=5: FPS: 3472 FrameTime: 0.288 ms
[loop] fragment-steps=5:fragment-uniform=true:vertex-steps=5: FPS: 2407 FrameTime: 0.416 ms
=======================================================
                                  glmark2 Score: 2451 
=======================================================
```
