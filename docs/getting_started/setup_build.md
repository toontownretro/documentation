# Toontown Retro Building / Setup Guide

Accurate as of

November 2021

Follow these steps to get everything built and set up on your computer.

Note: A line starting with **\>** is a command you should enter into the console.

1. Install required development tools

- Visual Studio 2019 ([https://visualstudio.microsoft.com](https://visualstudio.microsoft.com/)) **OR** Windows 10 SDK ([https://developer.microsoft.com/en-US/windows/downloads/windows-10-sdk](https://developer.microsoft.com/en-US/windows/downloads/windows-10-sdk))
  - For Visual Studio, make sure you check the option for **Desktop Development with C++**
- Strawberry Perl ([http://strawberryperl.com](http://strawberryperl.com/))
- FMOD Engine ([https://www.fmod.com/download](https://www.fmod.com/download))
  - Requires an account
  - Download is inside the FMOD Studio Suite section
- Python 3.6 or newer (64-bit version) ([https://www.python.org/downloads](https://www.python.org/downloads/))
- CMake ([https://cmake.org/download/](https://cmake.org/download/))
- Git for Windows ([https://git-scm.com/downloads](https://git-scm.com/downloads))
- Autodesk Maya 2016 or newer ([https://www.autodesk.com/](https://www.autodesk.com/)) with DevKit ([https://www.autodesk.com/developer-network/platform-technologies/maya](https://www.autodesk.com/developer-network/platform-technologies/maya))
  - Requires an account and license
  - TTMODELS requires Maya to be built
  - **Make sure you download the correct DevKit version!**
  - Additionally you need to add "MAYA\_ENABLE\_LEGACY\_VIEWPORT=1" to the Maya.env file (C:\Users\%USERPROFILE%\Documents\maya\20XX) for TTMODELS to include Vertex Shaders.
- Jom as an alternative to msbuild ([https://wiki.qt.io/Jom](https://wiki.qt.io/Jom))
- Clang as an alternative to the default Microsoft compiler ([https://github.com/llvm/llvm-project/releases](https://github.com/llvm/llvm-project/releases))
- Some good text editor, like Notepad++ ([https://notepad-plus-plus.org/downloads](https://notepad-plus-plus.org/downloads)) or Visual Studio Code ([https://code.visualstudio.com](https://code.visualstudio.com/))

● PYTZ (python -m pip install pytz) required for Robot Toon and the Level Editor

● PMW (python -m pip install Pmw) required for Robot Toon and the Level Editor

● ColoredLogs (python -m pip install coloredlogs) required for our custom OTP

● Semidbm (python -m pip install semidbm) required for our custom OTP

● Simplejson (python -m pip install simplejson) required for our custom OTP

● Pyyaml (python -m pip install pyyaml) required for our custom OTP

● Pytoml (python -m pip install pytoml) required for our custom OTP

- Pycryptodome (python -m pip install pycryptodome) required for OTP Authentication
- PyMysql/

● Compiled Astron (Alternative to our custom OTP)

1. Create and enter "player" directory

This directory is the home for all of the Toontown Retro projects, tools, and repositories.

\> mkdir %USERPROFILE%\player

\> cd %USERPROFILE%\player

The folder will be located at C:\Users\\<username\>\player. You can pin this folder to Quick Access if you want.

1. Clone WINTOOLS and setup environment

This tree contains the third-party packages and scripts to bootstrap your environment.

\> cd %USERPROFILE%\player

\> git clone [https://github.com/toontownretro/wintools](https://github.com/toontownretro/wintools)

Run the WINTOOLS setup script:

\> cd wintools\panda

\> setup

After you've finished running the setup script, look inside your player directory. You should see a vspec folder, a Config.pp file, a Config.prc file, a env.bat file, and a shortcut named Terminal.

- The vspec folder contains information required by the "attach" scripts, which you shouldn't have to modify.

- The Config.pp file contains instructions to configure the ppremake build system, such as where to locate the third-party packages. You can add additional instructions to this file to customize your local build.

- The Config.prc file contains runtime configuration variables when running Panda. The variables specified in this file are local to your computer. You can override existing variables or add new variables to this file as you see fit.

- The env.bat file sets needed environment variables when working in the Toontown Retro environment, similar to .bashrc. One important environment variable is %PLAYER%, which is set to the location of your player directory. You can add extra variables and commands to this file as you see fit.

- And finally, the Terminal shortcut launches a console window that automatically pulls in the commands and environment variables from env.bat when you launch it.

1. Edit env.bat and Config.pp

The env.bat file is the Batch script that automatically executes when you open the console using the Terminal shortcut. This file requires modifications to reference the correct paths to the Python and Maya installations on your computer. Open this file in a text editor, and correct the lines reading set PYTHON\_LOCATION= and set MAYA\_LOCATION= to the correct paths on your computer. Additionally, you may have to correct the location to the Visual Studio vcvars script, which sets up appropriate environment variables to invoke the Visual C++ compiler.

If you wish to use Clang as default compiler over the Microsoft Visual Studio one you will need to edit Config.pp and add the following line #define USE\_COMPILER Clang for msbuild/jom to respect that setting.

**Close your current console, and from now on, launch the console using the** Terminal **shortcut.**

1. Build WINTOOLS

This builds all of the third-party packages required to build the actual engine. **Note: You should now be using the** Terminal **console.**

First, you should attach to WINTOOLS. This adds appropriate variables to your environment that reference folders within WINTOOLS, such as the location of the built third-party packages.

\> cta wintools

Attaching to WINTOOLS (or any other tree for that matter) also sets an environment variable with the name of the tree that references the location of the tree. For instance, after attaching, %WINTOOLS% will be defined as C:\Users\\<username\>\player\wintools. Attaching to trees also adds the name of that tree onto another environment variable called %CTPROJS%, which contains a list of all the trees you are attached to. In this case, echo %CTPROJS% will print WINTOOLS:default. If you are attached to more than one tree, the tree names will be separated with a +. There is another command called ctshowprojs that you can run that will list your current attachments with nicer formatting than directly printing %CTPROJS%.

It is also convenient to place all of your attachment commands in env.bat, so launching the Terminal automatically attaches to the trees you need. I recommend doing that.

Now, you want to enter the WINTOOLS directory and build the third-party packages.

\> cd %WINTOOLS%

\> mkdir build && cd build

\> cmake .. -G "Visual Studio 16 2019" -A x64

\> cmake --build . -j --config RelWithDebInfo

It will take some time to build, but once it is done, all of the third-party packages will have been built and installed into %WINTOOLS%\built.

1. Copy WINTOOLS DLLs

All of the DLLs built by WINTOOLS are expected to be under a single directory, but the build process puts the DLLs for each third-party package in its own directory.

\> cd %WINTOOLS%
 \> python copy\_dlls.py

For the FMOD Sound System, the fmod.dll file has to be copied from C:\Program Files (x86)\FMOD SoundSystem\Fmod Studio API Windows\api\core\lib\x64 into the %WINTOOLS%\built\bin folder.

1. Clone and build ppremake

The rest of the trees rely on the ppremake build system. Before you build the engine, you will need to clone and build ppremake.

\> cd %PLAYER%

\> git clone [https://github.com/toontownretro/ppremake](https://github.com/toontownretro/ppremake)

After ppremake has been cloned, attach to it and build it.

\> cta ppremake

\> cd %PPREMAKE%

\> msbuild ppremake.sln -p:Configuration=Release;Platform=x64

The output of this is an executable named ppremake.exe, found in %PPREMAKE%\built\bin.

1. Clone and build DTOOL

We are now ready to start building the engine, starting with DTOOL. This tree contains low-level code that is needed by the rest of the engine and game trees.

First, clone the DTOOL repository.

\> cd %USERPROFILE%\player

\> git clone [https://github.com/toontownretro/dtool](https://github.com/toontownretro/dtool)

Attach to DTOOL, just like you did for WINTOOLS and ppremake.

\> cta dtool

Enter the DTOOL directory and let ppremake generate the scripts to build the tree.

\> cd %DTOOL%

\> ppremake

Finally, build the tree.

\> msbuild dtool.sln -m -t:install **or** jom install

After this completes, you should now have a fully built DTOOL tree in %DTOOL%\built.

The rest of the trees build in almost the exact same process, so not much more explanation is needed from here on down.

1. Clone and build PANDA

This tree contains the main part of the engine (graphics, audio, networking, etc). It relies on DTOOL.

\> cd %PLAYER%

\> git clone [https://github.com/toontownretro/panda](https://github.com/toontownretro/panda)

\> cta panda

\> cd %PANDA%

\> ppremake

\> msbuild panda.sln -m -t:install **or** jom install

After this completes, you should now have a fully built PANDA tree in %PANDA%\built.

1. Clone and build PANDATOOL

This tree contains various command line programs for use with PANDA, such as pview, maya2egg, etc.

\> cd %PLAYER%

\> git clone [https://github.com/toontownretro/pandatool](https://github.com/toontownretro/pandatool)

\> cta pandatool

\> cd %PANDATOOL%

\> ppremake

\> msbuild pandatool.sln -m -t:install **or** jom install

After this completes, you should now have a fully built PANDATOOL tree in %PANDATOOL%\built. You can verify this by running pview from the command line.

1. Clone and build DIRECT

This tree is a high-level Python interface built on top of PANDA. It contains the code for Actors, DistributedObjects, ShowBase, etc.

\> cd %PLAYER%

\> git clone [https://github.com/toontownretro/direct](https://github.com/toontownretro/direct)

\> cta direct

\> cd %DIRECT%

\> ppremake

\> msbuild direct.sln -m -t:install **or** jom install

1. Clone and build DMODELS

This tree contains various assets used by DIRECT.

\> cd %PLAYER%

\> git clone [https://github.com/toontownretro/dmodels](https://github.com/toontownretro/dmodels)

\> cta dmodels

\> cd %DMODELS%

\> ppremake

\> nmake install

Note the use of nmake instead of msbuild. msbuild is used to build code trees, while nmake is used to build model/asset trees.

Optionally, rather than nmake install to build DMODELS, you can enter nmake opt-pal. This will use the egg-palettize program in PANDATOOL to place lots of individual textures onto a smaller number of larger textures called palettes, which improves rendering performance. This also applies to TTMODELS.

1. Clone and build OTP

This tree contains code that is shared between Toontown and Pirates, such as the code for name tags.

\> cd %PLAYER%

\> git clone [https://github.com/toontownretro/otp](https://github.com/toontownretro/otp)

\> cta otp

\> cd %OTP%

\> ppremake

\> msbuild otp.sln -m -t:install

1. Clone and build TOONTOWN

This tree contains the actual Toontown game code, both client and server.

\> cd %PLAYER%

\> git clone [https://github.com/toontownretro/toontown](https://github.com/toontownretro/toontown)

\> cta toontown

\> cd %TOONTOWN%

\> ppremake

\> msbuild toontown.sln -m -t:install

1. Clone and build TTMODELS

This tree contains the Toontown assets. It can take hours to build from scratch (there's a lot of stuff).

The TTMODELS build process relies on a client/server mechanism to convert models out of Maya. This is done to speed up the build process by not having to contact the Maya license server for each model that needs to be converted.

Open a second console using the Terminal shortcut and start the Maya conversion server:

\> cta wintools

\> cta dtool

\> cta panda

\> cta pandatool

\> maya2egg\_server

Now, back in the first console, clone and start the TTMODELS build.

\> cd %PLAYER%

\> git clone [https://github.com/toontownretro/ttmodels](https://github.com/toontownretro/ttmodels)

\> cta ttmodels

\> cd %TTMODELS%

\> ppremake

\> nmake install

As noted in the DMODELS step, you can optionally use nmake opt-pal rather than nmake install if you want to build palettes.

After TTMODELS has been built, you need to use a special script

to remove the \_language references so the game can find all files correctly.

 Open the Terminal shortcut and use the following command.

\> cd %TTMODELS%\built & %PYTHON\_LOCATION%\python.exe ..\src\strip\_language.py

By default the script will ask to change all files ending with \_english in their name. If you builtTTMODELS in any other language than English you will need to manually write the language you built it in the Terminal.

1. Clone and build OTP\_SERVER (Optional)

This tree contains the code for the OTP server in which the client, AI and UD all connect too.

It's a requirement to run and play the game if you're not using Astron.

\> cd %PLAYER%

\> git clone [https://github.com/toontownretro/otp\_server](https://github.com/toontownretro/otp_server)

\> cta otp\_server

\> cd %OTP\_SERVER%

\> ppremake

\> msbuild otp\_server.sln -m -t:install

1. Running TOONTOWN and OTP

After all previous steps were completed the TOONTOWN and OTP trees are ready to be used for running the game.

Open four consoles using the Terminal shortcut and use the following commands, one for each Terminal.

If you're using Astron, Enter this command into the first terminal.

\> cd astron & astrond config/astrond.yml

Otherwise enter this command into the first terminal.

\> %PYTHON\_LOCATION%\python.exe -m otp\_server.realtime.main

Then after starting either Astron as your OTP or the custom OTP we provide, enter these three commands into the three other terminals you started.

\> %PYTHON\_LOCATION%\python.exe -m toontown.ai.AIStart

\> %PYTHON\_LOCATION%\python.exe -m toontown.uberdog.Start (OTP)

\> %PYTHON\_LOCATION%\python.exe -m toontown.uberdog.UDStart (Astron)

\> %PYTHON\_LOCATION%\python.exe -m toontown.toonbase.ToontownStart

Keep in mind, the pure developer code was from mid 2010 shortly after Victory Parties ended and because of that a lot of the late game content is missing such as, Field Offices, Toon Accessories, M.A.P.S and the Skip Button, which will be added from time to time.

Additionally, a lot of the code requires small changes to be compatible with Python 3 which may take time to be updated.