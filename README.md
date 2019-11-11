# Using platform io as an IDE for ASF4 projects
[ASF4](https://www.microchip.com/mplab/avr-support/advanced-software-framework) is a collection of software components such as peripheral drivers, middleware, and software applications provided by Microchip. The framework supports the Microchip's SAM family of microcontrollers.

[Atmel START](https://start.atmel.com/) is a web-based software configuration tool, which helps you get started with MCU development. Atmel START allows you to select and configure software components (from ASF4 and AVR Code), such as drivers and middleware to tailor your embedded application in a usable and optimized manner. Once an optimized software configuration is done, you can download the generated code project and open it in the IDE of your choice, including Studio 7, IAR Embedded Workbench®, Keil® μVision®, or simply generate a makefile.

This tutorial explains how to open an Atmel START project in platformIO IDE.

## Start a new project
1. Go to [Atmel START](https://start.atmel.com/) and create a new project. Choose your mcu and configure all the software libraries you need.
2. Save and Download the configuration file as a `[project name].atstart` in a new project folder.
3. Start a new platformio project in that project folder with `platformio init`
4. Create a custom board like explained [here](https://docs.platformio.org/en/latest/platforms/creating_board.html). You can start from the myboard.json file in the examples folder or another existing board. It is important to not have a "ldscript" tag defined in the "build" section. The atmel start project will contain a new linker script.
5. Define an [environment](https://docs.platformio.org/en/latest/projectconf.html) in platformio.ini file that contains at least the following settings:
```
[env:myEnvironment]
platform = atmelsam
board = myboard ; custom board as created in step 4
atstart_file = myAtStartProject.atstart ; name of the downloaded atstart file from step 3
lib_deps = https://github.com/JelleRoets/AtmelStart_PlatformIO ; library that will download and include all ASF libraries as configured in the atstart file
```
6. From the moment you save the ini file (or run `platformio run` for the first time) it will download the package in `.pio/libdeps/[env]/AtmelStart_PlatformIO/packages/[someHash]`
7. Go find a main.c file in that package and copy it to the project src folder as `main.cpp` and start editing.

Now you should be all set to further extend your project, compile, upload and debug your project with all features from platformIO.

## Examples
You can find an [ArduinoZeroASF](./examples/ArduinoZeroASF) example, where I use Atmel Start and ASF4 libraries to create and debug a blink example project for the Arduino Zero. You can simply do `platformio run` and / or ` platformio run --target upload ` (Note that uploading the binary with a debugger to the arduino zero will remove the existing bootloader). 

## Some background info
The magic happens by an extra script in the AtmelStart_PlatformIO. This script will download the package according to the given atstart file. It will include all headers and compile all library files when compiling the project. Everytime the .atstart file changes (when you updated your config in the web based atmel START tool) it will download a new package and use the (converted) .atstart file as a hash.

## TODO
- automatically update the custom board file when chosing a different mcu. It should not only update the mcu name but also the openocd name and target, find the corresponding svd file (e.g. downloading it from: http://packs.download.atmel.com/ ) and update the ram size 
- a bootloader offset is currently not taken into account, due to a bug (https://github.com/platformio/platformio-core/issues/3264) that prevents using custom board definitions in library scripts.
- add a custom task / command to easily go to the online Atmel START tool with the current .atstart file preloaded (not sure if there is an atmel start rest api for this...)

## Credits:
- [Frank Leon Rose](https://github.com/frankleonrose)
- [Jelle Roets](https://github.com/JelleRoets)