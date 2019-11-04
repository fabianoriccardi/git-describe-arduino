# Git-describe for Arduino
This script aims provide an automatic versioning system for Arduino sketches. It is based on git tag, to guarantee a strong relationship between source code and compiled firmware. Also, because name version will be visible from final binaries, this approach will force developers to maintain meaningful tags while developing the project.

## Features
 - it defines GIT_VERSION, available directly in your source code, containing version details
 - No extraordinary hacking to your ordinary toolchain (i.e. Arduino IDE)
 - Available for Window and Linux (MAC OS will be tested)

## Installation
  
  1. Clone this repository (e.g. you may locate it inside `/path/to/arduino-workspace/tools/`).
     ```
     git clone https://github.com/fabiuz7/git-describe-arduino.git
     ```

  2. On Linux, make script executable:  
     ```
     cd /path/to/arduino-workspace/tools/git-describe-arduino
     chmod 755 git-version.sh
     ```
    
     (no need to modify global path)  
     On Window, add git-version.bat to global path ([Add script to global path](https://docs.alfresco.com/4.2/tasks/fot-addpath.html)).

  3. Add this script to prebuild hooks of Arduino toolchain. Find the file `platform.txt` inside `/path/to/arduino-application/hardware/`. On Windows, append the following line:  
     ```
     recipe.hooks.sketch.prebuild.1.pattern=git-version.bat "{build.source.path}/src" "{build.path}/sketch/src"
     ```  
     On Mac/Linux, append the following line:  
     ```
     recipe.hooks.sketch.prebuild.1.pattern=/path/to/arduino-workspace/tools/git-version.sh "{build.source.path}/src" "{build.path}/sketch/src"
     ```

## Usage
To configure your sketch you need to complete few steps:

  1. Create an empty file called git-version.h in `src` folder inside your sketch folder (create if not exist). Leave the file empty because it is overwritten by the script.
  2. If no repository exist, initialize a new reposititory for the sketch and make the first commit.
  3. Modify your sketch to print the current version. An example:
      
     ```cpp
     #include "src/git-version.h"

     void setup() {
       Serial.begin(115200);
       Serial.println("Test git versioning");
       Serial.println(GIT_VERSION);
     }

     void loop() {
       // put your main code here, to run repeatedly: 
  
     }
     ```

  4. Compile and upload to see the result. Good versioning!

NOTE: GIT_VERSION is a string composed by 3 dash-separated fields:

 - {{lastest-git-tag}} printed only if a tag exists
 - [{{number-of-commits-since-latest-tag}}-{{last-commits-short-hash}}] printed only if the not equal to *0*
 - [dirty] printed only if working directory is dirty

Example: 0.3.0-8-gcc68a61-dirty

## Motivation
I was working of multiple projects simultaneously and it was difficult to remember the state of applications after months, especially when deployed on dmany microcontrollers. Hence, retrieving a precise version from compiled firmware became mandatory. Also this practise seems a rule of thumb in many environments such as NPM project ([git-describe](https://www.npmjs.com/package/git-describe)) or C# and visual studio ([GitInfo](https://www.nuget.org/packages/GitInfo/)).
