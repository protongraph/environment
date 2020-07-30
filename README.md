# Custom engine builds for ProtonGraph

**This is a dev repository.** If you are simply looking for the latest releases
of ProtonGraph, you can find them at this address:
https://github.com/protongraph/protongraph

**If you're trying to build the application yourself, keep reading.**


ProtonGraph is a "Godot game" as it was made using this engine. It is built on
**Godot 3.2.3** (at least) with a custom module that handle gltf import and
export. This means the export templates need to be recompiled as well.

This won't be necessary once we move to Godot 4.0 but until this next version
is stable, we'll remain on 3.2.x.

## Automated builds

The goal of this repository is to compile the custom engine and all the
export templates automatically for all three platforms (Linux, Windows and MacOS).
This is done through Github Actions and the binaries are then stored in their
own release. The ProtonGraph CI can fetch these binaries and use them to export
the project to all three platforms.

If you're developping new features for ProtonGraph and can't build the engine,
you can download the binaries directly from here.


## Building manually

### Install dependencies

Refer to this page depending on which OS you're using
https://docs.godotengine.org/en/stable/development/compiling/index.html


### Get all the repositories

+ Clone ProtonGraph
  - The master branch is usually the latest stable release
  - develop is the bleeding edge, untested version

```
git clone --recurse-submodules https://github.com/protongraph/protongraph
```

+ Clone the engine repository and make sure you're on the **3.2 branch**
```
git clone --branch 3.2 https://github.com/godotengine/godot
```

+ Go in the module folders and clone the extra modules
+ The gltf module default to the 4.0 branch but we want the master one
```
cd godot/modules
git clone --branch master https://github.com/godot-extended-libraries/gltf
```


### Compile the engine

+ Go back to the godot folder and launch this command.
  - Replace `x11` by your desired platform `x11 | windows | osx`
  - Feel free to increase the thread count if your CPU can handle it.
```
scons -j8 platform=x11
```

+ **Note about binaries size:**
  - Everytime you compile something with scons, you can chose to omit the
  debug symbols with the option `debug_symbols=no`. This creates significantly
  smaller binaries, at the expense of having a completely useless stack trace
  if something crashes.

### Compile the export templates

+ Still in the godot folder, launch the following commands
  - Replace `x11` by your desired platform `x11 | windows | osx`
  - Replace `bits=64` by `bits=32` if you're targetting an older 32bit hardware.

```
scons platform=x11 tools=no target=release_debug bits=64
scons platform=x11 tools=no target=release bits=64
```

### Compile the GDNative libraries
+ In ProtonGraph repository folder
``` bash
cd src/native

# edit the following command to use the proper platform
./compile_all.sh linux release
```

### Export the project

#### Using the command line
+ Make sure the export preset contains the path to the export templates,
  relative to the project directory.
+ Call this command with the proper parameters:
  - `godot.x11.tools.64` is the editor binary.
  Replace with the one matching your platform.
  - `-export "linux"` linux is the name of the preset. Pick one among `linux`,
  `osx` and `windows`. They're defined in `export_preset.cfg`.
  - `build/ProtonGraph` is the name of the exported binary. Make sure the
  destination folder exists before running the command.
```
./godot.tools.64 --path path/to/project --export "linux" "build/ProtonGraph"
```


#### Using the GUI

+ Start your custom engine binary (located in `godot/bin`)
+ Open the ProtonGraph project
+ Click **Project > Export**
+ Select the preset matching your target platform
+ Select an export path. This is where the final executable will be created

![image](https://user-images.githubusercontent.com/52043844/88791147-8896b100-d199-11ea-84e5-f2ae1bdaf107.png)

+ Assign the two export templates in the bottom right of the **Options** tab
  - Export templates are also located in `godot/bin`
+ Click on **Export Project**
