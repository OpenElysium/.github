# Open Elysium

Preserving and modernizing *Vampire: The Masquerade – Bloodlines* (Troika
Games, 2004) by rebuilding it from source.

Open Elysium is a matching decompilation of Bloodlines' retail 1.2 release.
Each of the game's DLLs (the game logic, the Source engine and its libraries,
and the embedded Python) is rewritten as C/C++ source that compiles, with the
original compiler (Visual C++ 6.0 SP5 + Processor Pack), into a byte-for-byte
copy of the shipped binary.

## Goals

1. **Own every module's code.** Decompile every retail 1.2 DLL to matching
   source. Retail behaviour stays as shipped, bugs included.
2. **Run the game on it.** Each finished module builds from source and
   replaces the retail DLL in a working install. The game runs from the
   original data files with the source-built DLLs.
3. **Fix the game's bugs in source.** Bug fixes go into a separate build, so
   the matching build stays identical to retail. The first bug list is the
   Unofficial Patch's run-time fixes, each rewritten as a source change,
   followed by findings from our own analysis of the retail binaries.
4. **Port and modernize.** Use the same source for new renderer backends, a
   64-bit build, uncapped frame rates and widescreen support. Third-party
   middleware gets open replacements (minimp3 for Miles audio, current zlib,
   FFmpeg for Bink video), and saves stay compatible across the retail,
   fixed and 64-bit builds.

## Status

The project tracks 17 DLLs. Five are accepted, meaning they're built from
source and replace the retail DLL in the game: `vampire_python21`, `tier0`,
`filesystem_stdio`, `vstdlib` and `launcher`. Work is now on `vampire.dll`
(the game logic, about 94% of functions matched), and the engine, physics,
client, rendering and UI modules are in progress.

## Tech stack

Development happens on Windows, with the original toolchain for the code that
has to match and modern tools for everything around it.

| Tool | Role |
|---|---|
| **Visual C++ 6.0 SP5 + Processor Pack** | The compiler, linker and resource compiler retail was built with. Matching only works with this exact build (compiler 12.00.8804, Rich header IDs 9044 for the compiler and 8447 for the linker). SP6 and newer compilers produce different code, so they can't be used. |
| **Python + [uv](https://docs.astral.sh/uv/)** | All project tooling: compiling a function and comparing its bytes against retail (with capstone and pefile), linking and comparing whole DLLs, and building and installing the source-built modules. |
| **Visual Studio with clang-cl (x86)** | Modern 32-bit builds: struct and class layout dumps, and difftests that run a source function and its retail counterpart on the real CPU and compare the results. |
| **Ghidra** | Analysis of the retail binaries: function bounds, types, vtables and cross-module references. |
| **Bloodlines retail 1.2 + Unofficial Patch** | The reference binaries to match, and the install the source-built DLLs run in. |
| **WinDbg / cdb** (optional) | Debugging crashes in test runs. |

### How a module gets done

1. Each function is written in C/C++, compiled with VC6, and compared byte
   for byte against the retail function.
2. Once its functions match, the module's source is linked into a full DLL
   with VC6 and compared against the retail DLL as a whole.
3. The built DLL replaces the retail one in a copy of the install, and the
   game runs on it.

## Note on game content

The game itself isn't distributed here. You need a legitimate copy of
*Vampire: The Masquerade – Bloodlines*, and all game data comes from your own
install.
