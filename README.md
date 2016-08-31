
Salto for the RetroWeb Vintage Computer Museum
============================================

This repository contains the JavaScript version of Salto used in the [retroweb-vintage-computer-museum](https://github.com/marciot/retroweb-vintage-computer-museum). This JavaScript port of Salto provides the
emulation core for the Xerox Alto.

This code is derived from Juergen Buchmueller's [SALTO](https://github.com/brainsqueezer/salto_simulator) which is written in ANSI C.

## How does this code differ from SALTO's distribution?

1. Added headers to enable the code to compile.
2. Modified MAKEFILE for Emscripten build
3. Modified main loop to be compatible with EMSCRIPTEN
4. Replaced the sdl_blit function with my own implementation (the original was crashing in Emscripten)
5. Added relative mouse motion code to allow the emulator to work when HTML5 pointerlock is enabled
6. Disabled the GUI icons when compiling to Javascript
7. Added SDL keysyms which are missing from libSDL in EMSCRIPTEN
8. Added vsync callback to allow browser refresh to coincide with vsync.

## Build instructions

Make sure you have a working version of Emscripten. Do this by following the [Emscripten tutorial](https://kripken.github.io/emscripten-site/index.html).

Make sure your Emscripten toolchain is up to date by executing the following commands:

```
./emsdk update
./emsdk install latest
./emsdk activate latest
source ./emsdk_env.sh
```

Use the following commands to clone the emulator code and build the emulators:

```
git clone https://github.com/marciot/retroweb-salto-simulator-js.git
cd retroweb-salto-simulator-js

# First build natively (this is so some of the helper tools get built)
export EMSCRIPTEN=0
make clean
make

# Then rebuild salto using EMSCRIPTEN
export EMSCRIPTEN=1
export OPTIMIZATION_FLAGS="-O3"
export CFLAGS="$OPTIMIZATION_FLAGS"
export EMCC_OPTS="$OPTIMIZATION_FLAGS"
emmake make clean
emmake make

# Finishing steps
mv bin/salto bin/salto.bc
emcc bin/salto.bc $EMCC_OPTS -o salto.js -s EXPORTED_FUNCTIONS='["_main"]'
sed salto.js -i -e 's/function _SDL_CreateRGBSurfaceFrom/function _SDL_CreateRGBSurfaceFrom_disabled/'
```

The "salto.js" and "salto.js.mem" files can be used as drop in replacement for the ones in the
"emulators/salto-alto" directory of the
[retroweb-vintage-computer-museum](https://github.com/marciot/retroweb-vintage-computer-museum) distribution.
