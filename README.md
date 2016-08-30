
Salto for the RetroWeb Vintage Computer Museum
============================================

This repository contains the JavaScript version of Salto used in the [retroweb-vintage-computer-museum](https://github.com/marciot/retroweb-vintage-computer-museum). This JavaScript port of Salto provides the
emulation core for the Xerox Alto.

This code is derived from Juergen Buchmueller's [SALTO](https://github.com/brainsqueezer/salto_simulator) which is written in ANSI C.

## How does this code differ from SALTO's distribution?

1. Added headers to enable the code to compile.
2. Added shims for missing SDL components.
2. The main loop is modified so that it can be run by Emscripten.
3. The MAKEFILE has been hacked to work with "emmake" (it probably broke it so it will not compile natively anymore)

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
git clone https://github.com/marciot/retroweb-salto.git
cd retroweb-salto

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

The "salto.js" file can be used as drop in replacement for the one in in the "emulators/salto-alto" directory of the
[retroweb-vintage-computer-museum](https://github.com/marciot/retroweb-vintage-computer-museum) distribution.
