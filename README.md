# hummingbird

Hummingbird is a proposition for an extensible buildfile generation system that allows entirely abstracting the necessity for scripting.

It is comparable to CMake in its role, however differs greatly in execution.

## Purpose 

Hummingbird at its core serves one purpose: processing a configuration file into variables, and converting those variables into a build script.

## Applications

This project is written with the goal of replacing and reworking major components in [dragon](https://dragon.krit.me)

It's also written with a drastic restructure of [theos](https://theos.dev) in mind.

Other applications:
* Eliminates the last big roadblock in compiling for iOS on linux.
* Building a project written for one build system with a different one.
* Being used in an existing build system to give it superpowers

## Project architecture

Hummingbird should consist of four major components:
* Configuration Loader; responsible for understanding different buildsystem formats
* Abstraction Layer; modular representation of project configurations
* Abstraction Processor; responsible for processing that abstraction
* Buildscript generator; responsible for generating a buildscript from the processed abstraction

### Configuration Loader

The configuration loader should be adaptable to allow support to be written for (almost) any existing buildsystem.

The configuration loader serves the purpose of taking in different configuration formats and processing them into Hummingbird's core format, for the sake of creating the Abstraction Layer

#### Requirements

The loader should be able to process the following:
Makefiles
Ninja.build files
CMake Files
Xcode Project Configuration Files

**It should not rely on any external tools to do so.**

### Abstraction Layer

Utilization of object oriented design philosophy, even in procedural languages, should be done here, to create a representation of the Build Target Project before processing elsewhere.

This allows hummingbird to be used as a standalone Configuration parser by other projects, instead of limiting it to only buildfile generation

### Abstraction Processor

This is the component responsible for taking our Abstraction and processing it for the BuildScript Genrator

##### Abstraction Processing Modes

**Translated**

This format assumes Hummingbird is being used within a build system, and is being ran directly before every build.

It should limit the logic and evaluation it uses only to tasks it can perform faster.

**Readable**

This mode should generate its output in a human readable, human editable format. Variables should not be optimized, Sections clearly divided, so on.

It should do its best to intelligently understand the build process and its outcomes, and abstract that where possible.

**Optimized**

This mode should generate a script for the build system that eliminates as much build-time logic as physically possible.

This mode should assume hummingbird will be ran once and the target script reused.

It should be written with lower level quirks of the targeted build system in mind.


### Buildscript generator

The Buildscript generator should be, essentially, the opposite of the Loader. It's responsible for taking the Abstraction Layer's representation and transcribing that in the chosen format. 

#### Requirements

The generator should offer output in common script formats. 

GNU Make and ninja should be supported in Hummingbird itself, following CMake's generator


##### Option to produce its output as C source

This is a somewhat niche utility but one I find incredibly useful.

The generator should be able to optionally produce C source that serves as the absolute machine-code minimum to compile the product.

This is done with products like (Procursus)[https://github.com/ProcursusTeam/Procursus] in mind, eliminating Hummingbird as a requirement for people building from source and not contributing to the project.

It will likely use ninja-build's source code for proper job management and speed/no-ops, but removing the configuration loading stage and hard-coding the values in the source.
