# Git-describe for Arduino

This script provides an automatic versioning system for Arduino sketches based on git tag. This approach guarantees a strong relationship between source code and the compiled firmware. Moreover, it will enforce the developer to maintain meaningful tags during the project development.

  :information_source: **NOTE**: Currently this script is incompatible with the brand new Arduino IDE 2.x, but a [feature request](https://github.com/arduino/arduino-cli/issues/999) is already pending.

## Features

- it defines the constant GIT_VERSION, visible in your source code, containing version details
- No extraordinary hacking to Arduino IDE
- Available for Window and Linux (MAC OS will be tested)

## Requirements

- Arduino IDE 1.8.9, 1.8.11 or newer
- Git

> NOTE: on Arduino IDE 1.8.10 the script doesn't work because a regression bug. I didn't test this method on versions older than 1.8.9.

## Installation
  
  1. Clone this repository (e.g. you may locate it inside `/path/to/arduino-workspace/tools/`).
     ```
     git clone https://github.com/fabiuz7/git-describe-arduino.git
     ```

  2. **On Linux**, make script executable:  
     ```
     cd /path/to/arduino-workspace/tools/git-describe-arduino
     chmod 755 git-version.sh
     ```
    
     (no need to modify global path)  
     
     **On Window**, add git-version.bat to global path ([Add script to global path](https://docs.alfresco.com/4.2/tasks/fot-addpath.html)).

  3. Add this script to prebuild hooks of Arduino toolchain. Created if not exist the file `platform.txt` inside `/path/to/arduino-application/hardware/`. On Windows, append the following line:  
     ```
     recipe.hooks.sketch.prebuild.1.pattern=git-version.bat "{build.source.path}/src" "{build.path}/sketch/src"
     ```  
     On Mac/Linux, append the following line:  
     ```
     recipe.hooks.sketch.prebuild.1.pattern=/path/to/arduino-workspace/tools/git-version.sh "{build.source.path}/src" "{build.path}/sketch/src"
     ```

## Usage

To configure your sketch you need to complete few steps:

  1. Create `src` folder in the sketch folder.
  2. Create `git-version.h` in `src` folder. Leave the file empty because it is overwritten by the script.
  3. If your repository is not under git, initialize a reposititory for the sketch and do the first commit.
  3. Modify your sketch to include `git-version.h` and print the current version. Example:
      
     ```cpp
     #include "src/git-version.h"

     void setup() {
       Serial.begin(115200);
       Serial.println();
       Serial.print("Git version: ");
       Serial.println(GIT_VERSION);
     }

     void loop() {
       // put your main code here, to run repeatedly: 
  
     }
     ```

  4. Compile and upload to see the result. Good versioning!

NOTE: GIT_VERSION is a string composed by 3 dash-separated fields:

- `lastest-git-tag`: printed if a tag exists
- `number-of-commits-since-latest-tag`-`last-commits-short-hash`: printed if `number-of-commits-since-latest-tag` is not *0*
- "dirty": printed if the working directory is dirty

Example: 0.3.0-8-cc68a61-dirty

## Motivation

I was working on multiple projects simultaneously and it was difficult to remember the state of applications after a few months, especially when firmware were deployed on many microcontrollers. Hence, retrieving the precise version from compiled firmware became mandatory. Also, this practice seems a rule of thumb in many toolchains and environments. This script took inspiration from similar projects such as [git-describe](https://www.npmjs.com/package/git-describe) for JavaScript and NPM and [GitInfo](https://www.nuget.org/packages/GitInfo/) for C# and Visual Studio.
