# Python in the browser

The aim of this project is to make Python usable in the browser. This is done
using the standard Python implementation (CPython) compiled with emscripten to
generate an asm.js library.

The benefits of this approach are:

 * This gives access to a full Python implementation.
 * Asm.js can be pretty fast, so the interpreter is still quite useful.
 * It supports existing extension modules.
 * It supports Cython for speeding up the code and binding to C/C++.
 
If you have a Python application using SDL (1 or 2) and maybe OpenGLES 2 then
this project could help you deliver it via the browser.

# Prerequesits

The build requires a complete development toolchain for the build system. This
is used to build the portions of Python required for bootstrapping the complete
Python build.

Emscripten is also required for the cross compilation. Version 1.36.0 has been
tested.

Some of the examples use Cython which can be installed with pip. Versions
0.20.1 and above have been used successfully.

---

## Emscripten 1.38.48 have been tested, 1.39.0 and later seem to fail

# compiling linux

Download https://github.com/emscripten-core/emsdk
And needed libraries for python, see https://devguide.python.org/setup/#install-dependencies
Atm, requires gcc/g++ multilib for x32 host parser

Install and activate 1.38.48 emsdk
```
./emsdk install 1.38.48
./emsdk activate 1.38.48
source <EMSDK path>/emsdk_env.sh
```

enter cpython-emscripten/3.5.2 and run
```
make
```

And you should have a nice static wasm32-wasi libpython3.5.a in installs/python-3.5.2/



# Credits

Many of the patches and instructions are derived from the excellent work of
Marat Dukhan on the [EmCPython](https://github.com/PeachPy/EmCPython) project.

# Python Versions

## 3.5.2


