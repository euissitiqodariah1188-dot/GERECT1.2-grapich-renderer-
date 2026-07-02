# GERECT1.2-grapich-renderer-
open source grapich renderer you can use it to make your own grapich renderer AND YOU NEED MSYS2 MINGW 
heres how to use it

1 create file.c or cpp

2 compile it in msys2 minGW 
cd "/c/Users/Dede/Downloads/GERECT1.2/GERECT1.2"
make

3 finish

--how to use obj file in gerect

1 create c file 
Then write this in the file.
#include "ger/ger.h"
#include "gersys64/gersys64.h"
#include "ger3d/ger3d.h"
#include "ger3d/gerlight.h"
#include "gerui/gerui.h"

int WINAPI WinMain(HINSTANCE hInstance, HINSTANCE h2, LPSTR cmd, int n) {
    (void)h2; (void)n;

    gerInit();

    GerScene scene;
    gerSceneInit(&scene, 1024, 768, 0);

    /* setup lighting */
    GerLightSystem ls;
    gerLightSystemInit(&ls);
    GerLight sun = gerLightMakeDirectional(
        gerVec3Make(-0.5f, -1.0f, 0.5f),
        gerVec3Make(1.0f, 0.95f, 0.8f), 1.2f);
    sun.castShadow = 1;
    gerLightAdd(&ls, sun);
    gerLightAdd(&ls, gerLightMakeAmbient(
        gerVec3Make(0.15f, 0.15f, 0.2f), 1.0f));
    gerSceneSetLightSystem(&scene, &ls);

    /* load OBJ -  */
   const char *objPath = (cmd && cmd[0]) ? cmd : "examples/your_namefile.obj";
    GerLayout *model = gerFugLoadObj(&scene, "model", objPath, NULL);
    if (!model) {
        MessageBoxA(NULL, "Gagal load OBJ! Cek path file.", "Error", MB_ICONERROR);
        gerSceneDestroy(&scene);
        gerLightSystemDestroy(&ls);
        gerShutdown();
        return 1;
    }

    /* setup camera */
    scene.camera.eye    = gerVec3Make(3.0f, 2.5f, -5.0f);
    scene.camera.target = gerVec3Make(0.0f, 0.0f,  0.0f);

    /* create window */
    GerWindow win;
    gerWindowCreate(&win, hInstance, "GERECT 1.2 - OBJ Test", 1024, 768);
    gerWindowBindScene(&win, &scene);

    double startTime = gerGetTimeSeconds();

    while (gerWindowPollEvents(&win)) {
        gerWindowApplyPendingResize(&win);

        /* rotasi model tiap frame biar keliatan 3D-nya */
        double t = gerGetTimeSeconds() - startTime;
        gerLayoutSetRotation(model, 0.0f, (float)(t * 45.0), 0.0f);

        gerLightSystemShadowPass(&ls, &scene);
        gerSceneRenderFrame(&scene);
        gerWindowPresent(&win);
        gerSleepMs(1);
    }

    gerWindowDestroy(&win);
    gerLightSystemDestroy(&ls);
    gerSceneDestroy(&scene);
    gerShutdown();
    return 0;
}
2 then compile in msys2 minGW
gcc -Wall -O2 -std=c11 -Iinclude examples/test_obj.c \
    -o test_obj.exe -Lbuild -lgerect \
    -lgdi32 -luser32 -lkernel32 -lwinmm -mwindows

3 run it ./your_namefile_obj.exe


