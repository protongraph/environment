# Custom engine builds for ConceptGraph

**This is a dev repository.** If you are simply looking for the latest releases of ConceptGraph, you can find them at this adress: https://github.com/HungryProton/concept_graph

**If you're trying to build the application yourself, keep reading.**


ConceptGraph is a "Godot game" as it was made using this engine. It is built on **Godot 3.2.3** (at least) with a custom module
that handle gltf import and export. This means the export templates need to be recompiled as well.

## Automated builds

**WORK IN PROGRESS**

The goal of this repository is to compile the custom engine and all the templates automatically for all three platforms (Linux, Windows and MacOS).
This is done through Github Actions and the artifacts should be stored somewhere in here. Once this is done at least once, the ConceptGraph
repository can fetch these binaries and use them to export the project.
 
 
## Building manually

### Install dependencies

Refer to this page depending on which OS you're using
https://docs.godotengine.org/en/stable/development/compiling/index.html


### Get all the repositories

+ Clone ConceptGraph
  - For now: **you need to use the standalone branch**
  - In the future:
    + The master branch is usually the latest stable release
    + develop is the bleeding edge, untested version

```
git clone https://github.com/HungryProton/concept_graph
```


+ Clone the engine repository and make sure you're on the **3.2 branch**
```
git clone --branch 3.2 https://github.com/godotengine/godot
```

+ Go in the module folders and clone the extra modules
```
cd godot/modules
git clone https://github.com/godot-extended-libraries/gltf
```


### Compile the engine

+ Go back to the godot folder and launch this command.
  - Replace `x11` by your desired platform `x11 | windows | osx`
  - Feel free to increase the thread count if your CPU can handle it.
```
scons -j8 platform=x11
```

### Compile the export templates

+ Still in the godot folder, launch the following commands
  - Replace `x11` by your desired platform `x11 | windows | osx`
  - Replace `bits=64` by `bits=32` if you're target an older 32bit hardware.

```
scons platform=x11 tools=no target=release_debug bits=64
scons platform=x11 tools=no target=release bits=64
```

### Compile the GDNative libraries
+ In ConceptGraph repository folder
``` bash
cd src/native
git submodule update --init godot-cpp
cd godot-cpp
git submodule update --init godot_headers

# edit the following command to use the proper platform
scons platform=linux generate_bindins=yes -j8 bits=64

cd ../

# Same here
scons platform=linux bits=64
```

### Export the project

#### Using the command line
 **TODO**

#### Using the GUI

+ Start your custom engine binary (located in `godot/bin`)
+ Open the ConceptGraph project
+ Click **Project > Export**
+ Select the preset matching your target platform
+ Select an export path. This is where the final executable will be created

![image](https://user-images.githubusercontent.com/52043844/88791147-8896b100-d199-11ea-84e5-f2ae1bdaf107.png)

+ Assign the two export templates in the bottom right of the **Options** tab
  - Export templates are also located in `godot/bin`
+ Click on **Export Project**
