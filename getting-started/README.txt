TOPAS Version 3.6.1+

All use of TOPAS is governed by the TOPAS License Agreement provided in this directory as LICENSE.txt.

This README shows how to install and run TOPAS with and without user extensions.

In case of problems, see the TOPAS Monte Carlo User Forum at:
topasmc.org

These instructions are written for single user installations.
Systems administrators performing multi-user installations may need to adapt these recipes.

These instructions assume the user has basic familiarity with the use of paths, shells and environment variables on their chosen operating system.
For users who have that familiarity, it should be obvious how to adapt these recipes to their own installations.
For others who do not have that familiarity, we suggest you first read some general tutorials for paths, shells and environment variables.

Please read the instructions to install TOPAS on https://topas.readthedocs.io/
Below are instructions to add TOPAS-nBio to your TOPAS application.

To add User Extensions:
You will need a tool called CMake (version 3.3 or newer).
Type "which cmake" to see if you already have this tool
and "cmake -version" to see what version you may have.

If you need to install CMake, install from a binary distribution at:
http://www.cmake.org/cmake/resources/software.html
Run the cmake.app
and follow the instructions in CMake's menu item: "Tools"... "How to Install for Command Line Use"

Mac Users: You must have Mac OS X 10.12 or newer.
Users of earlier OSX versions or those using the above OSX versions with earlier Xcode versions
can run the pre-built TOPAS, but can not add extensions.
Install the Xcode compiler, version 8.3.2 or newer, from:
https://developer.apple.com/xcode/downloads/

Linux Users: Check that you have an appropriate version of the C++ compiler
You will need gcc >=4.8.3
To check your gcc version, gcc --version
If your gcc version is not sufficient, ask your sysadmin if there is already a newer version in some non-default installation on your system. They may be able to tell you how to switch to that version.
Otherwise, you will need to install a newer version or ask your sysadmin to do so.
Then locate the libstdc++.so file and add this directory to your LD_LIBRARY_PATH environment variable
(`export LD_LIBRARY_PATH=/path/to/gcc/lib:$LD_LIBRARY_PATH`).

Unzip the full set of Geant4 header files from topas/Geant4Headers.zip.
You should end up with a new directory: topas/Geant4Headers.
Do not use headers from any other version of Geant4 as they could appear to run OK but give wrong results!

Place your extra TOPAS code into a directory that is NOT inside of the topas directory.
For example, you might have TOPAS in:
/Applications/topas
and the extensions in:
/Applications/topas_extensions

Mac: cd /Applications/topas
Linux: cd ~/topas

cmake -DTOPAS_EXTENSIONS_DIR=/Applications/topas_extensions
make -j8

In the make statement, the value after -j, such as -j8 indicates how many threads make should use at once.
This depends on your computer's capabilties. A typical quad-core machine with hyperthreading can run efficiently with 8 threads.

Don't worry about any "Warning" messages. Only worry about messages that say "Error".

If you get any error messages, confirm that you unzipped the Geant4 header files as explained above.
Also check that you have an appropriate compiler (see notes earlier in this section).

Then set up the environment and run as in steps 3 and 4 above.

CMake caches the name of your extensions directory and watches for subsequent changes there. If you make changes to any of the extensions you were already including (such as while you are debugging your extensions), you just need to re-run
make -j8

If you add additional extensions to your extensions directory, again run the full
cmake -DTOPAS_EXTENSIONS_DIR=/Applications/topas_extensions
make -j8
