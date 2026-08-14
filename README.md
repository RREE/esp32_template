# ESP32 Ada Project Template (ESP-IDF Integrated)

This repository provides a template for integrating Ada source code into
the ESP-IDF (C-based) build system, targeting the original **ESP32**
(Xtensa **LX6**). It's a sibling to
[godunko/esp32s3_template](https://github.com/godunko/esp32s3_template)
(ESP32-S3 / Xtensa LX7) — the two chips need different runtime-description
files and toolchain core-config selection, so they're separate templates
rather than one parameterized by chip.

## Project Architecture

Instead of a standalone Ada executable, this project compiles Ada source
into an encapsulated static library that is linked into the final ESP-IDF
project.

* Ada side: managed by Alire (`alr`).
* System side: managed by ESP-IDF (`CMake`/`ninja`).

## Status

Validated end to end on real hardware (ESP32-D0WD-V3, rev v3.1) with
ESP-IDF v6.0.2. Currently depends on three forks carrying LX6 support not
yet merged upstream:

* [`RREE/a0b-tools`](https://github.com/RREE/a0b-tools) — LX6 architecture
  branch. Tracking PR: [godunko/a0b-tools#2](https://github.com/godunko/a0b-tools/pull/2)
* [`RREE/espidf_gnat_runtime`](https://github.com/RREE/espidf_gnat_runtime) —
  `esp32.svd` / `runtime-esp32.json` / `a-intnam__esp32.ads`. Tracking PR:
  [godunko/espidf_gnat_runtime#3](https://github.com/godunko/espidf_gnat_runtime/pull/3)
* [`RREE/xtensa-dynconfig`](https://github.com/RREE/xtensa-dynconfig) —
  points the Xtensa GCC core-config plugin at the LX6 variant instead of
  the hardcoded LX7 one. Not yet a PR — the fix needs redesigning first so
  it doesn't just move the bug onto S3 users; see the tracking issue (TBD)
  for details.

Once these land, this template's `.gitmodules` should be repointed at
`godunko`'s repos directly and `crates/xtensa-dynconfig`'s pin at whatever
the fixed default/config mechanism turns out to be.

## Prerequisites

* ESP-IDF SDK: version 6.x recommended. Ensure `idf.py` is on your PATH.
* Alire (Ada Libre Resources): the Ada package manager.

## Build Instructions

1. Setup environment

   ```bash
   # Linux/macOS
   . $HOME/esp/esp-idf/export.sh
   ```

2. Clone and build tools

   ```bash
   git clone --recurse-submodules https://github.com/RREE/esp32_template.git my_esp32_project
   cd my_esp32_project
   alr -C crates/a0b-tools/ build
   alr -C crates/xtensa-dynconfig/ build
   ```

3. Build, flash, and monitor via ESP-IDF

   ```bash
   # Configure the target (first time only)
   idf.py set-target esp32

   # Build the full project (compiles and links Ada library + ESP-IDF components)
   idf.py build

   # Flash and monitor
   idf.py flash monitor
   ```

   You should see:

   ```
   Hello, Ada world!

   This application tests few features of the Ada runtime
   Feel free to replace it by your application!
   ```

`ESP-IDF` and `Ada & SPARK` extensions for VS Code create a useful
development environment.

## Related repositories

* [ESP-IDF GNAT Runtime](https://github.com/godunko/espidf_gnat_runtime)
* [Ada/ESP-IDF Binding](https://github.com/godunko/espidf)
* [esp32s3_template](https://github.com/godunko/esp32s3_template) — the
  sibling template this one was adapted from, for ESP32-S3/LX7
