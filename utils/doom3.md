`F_OK` patch 

https://bugs.gentoo.org/741490

```
--- a/src/video/SDL_egl.c
+++ b/src/video/SDL_egl.c
@@ -45,6 +45,10 @@
 
 #if SDL_VIDEO_DRIVER_RPI
 /* Raspbian places the OpenGL ES/EGL binaries in a non standard path */
+#include "raspberry/SDL_rpievents_c.h"
+#include "raspberry/SDL_rpiopengles.h"
+#include "raspberry/SDL_rpimouse.h"
+#include "raspberry/SDL_rpivideo.h"
 #define DEFAULT_EGL ( vc4 ? "libEGL.so.1" : "libbrcmEGL.so" )
 #define DEFAULT_OGL_ES2 ( vc4 ? "libGLESv2.so.2" : "libbrcmGLESv2.so" )
 #define ALT_EGL "libEGL.so"

--- a/src/video/raspberry/SDL_rpivideo.h
+++ b/src/video/raspberry/SDL_rpivideo.h
@@ -41,6 +41,12 @@
     DISPMANX_DISPLAY_HANDLE_T dispman_display;
 } SDL_DisplayData;
 
+
+typedef struct {
+    DISPMANX_ELEMENT_HANDLE_T element;
+    int width;
+    int height;
+ } EGL_DISPMANX_WINDOW_T;
 
 typedef struct SDL_WindowData
 {
```
