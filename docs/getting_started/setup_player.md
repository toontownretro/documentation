# Toontown Retro Building / Setup Guide
Follow these steps to generate your development environment & main workspace.
In the future, this guide may be split up into different pages for installing specific programs, such as TOONTOWN.

## NOTICE
- In order to build the asset pipeline, ``TTMODELS``, you will need a Autodesk Maya license. If you are currently a student, you can sign up for the free student license.
- The installation requires you to build modules. Building modules with ``msbuild`` will consume most of your CPU usage, which may cause your computer to freeze during the build process. Libraries such as ``panda`` and ``TTMODELS`` may take more than an hour individually to build.
- This installation guide is for Windows only.

## Required Development Tools
- [Visual Studio 2019](https://visualstudio.microsoft.com/) **OR** [Windows 10 SDK](https://developer.microsoft.com/en-US/windows/downloads/windows-10-sdk)
  - For Visual Studio, make sure you check the option for **Desktop Development with C++**
- [Strawberry Perl](http://strawberryperl.com/)
- [Python 3.6 or newer (64-bit version)](https://www.python.org/downloads/)
  - This will be the primary Python interpreter used. 
- [CMake](https://cmake.org/download/)
- [Git for Windows](https://git-scm.com/downloads)
- Some good text editor, like [Notepad++](https://notepad-plus-plus.org/downloads) or [Visual Studio Code](https://code.visualstudio.com/)
- *Optional:* [Jom](https://wiki.qt.io/Jom) as an alternative to msbuild
- *Optional:* [Clang](https://github.com/llvm/llvm-project/releases) as an alternative to the default Microsoft compiler

## Required Third Party Packages\*
- [Autodesk Maya 2016 or newer](https://www.autodesk.com/)
  - Asset pipelines, such as TTMODELS, requires Maya to be built with Panda
  - Requires an account and license
  - Additionally you need to add ``MAYA_ENABLE_LEGACY_VIEWPORT=1`` to the Maya.env file located in ``C:\Users\%Username%\Documents\maya\20XX`` for TTMODELS to include Vertex Shaders.
- Autodesk Maya DevKit
  - **Make sure you download the correct DevKit version!**
  - [DevKit (Maya 2016)](https://github.com/sonictk/Maya-devkit/tags)
  - [DevKit (Maya 2019+)](https://www.autodesk.com/developer-network/platform-technologies/maya)
- [FMOD Engine](https://www.fmod.com/download)
  - Requires an account
- [Steam Audio Library](https://drive.google.com/file/d/1oPQyUFJ0lk6Jx8vPAHA1USx6hafIe_B7/view?usp=share_link)
  - Extract the steamaudio folder into the user folder (``C:\Users\%Username%``)


## Creating The Development Environment
By default, the workspace will be located at ``C:\Users\%username%\player``
This directory is the home for all of the Toontown Retro projects, tools, and repositories. You can pin this folder to Quick Access if you want.
```
mkdir %USERPROFILE%\player
cd %USERPROFILE%\player
```

### Configuring the Setup Environment
#### Cloning WINTOOLS
This tree contains the third-party packages and scripts to bootstrap your environment.
```
cd %USERPROFILE%\player
git clone https://github.com/toontownretro/wintools
cd wintools\panda
setup
```

After you've finished running the setup script, look inside your player directory. You should see a vspec folder, a Config.pp file, a Config.prc file, a env.bat file, and a shortcut named Terminal.
- The **vspec** folder ontains information required by the "attach" scripts, which you shouldn't have to modify.
- The **Config.pp** file contains instructions to configure the ppremake build system, such as where to locate the third-party packages. You can add additional instructions to this file to customize your local build.
- The **Config.prc** file contains runtime configuration variables when running Panda. The variables specified in this file are local to your computer. You can override existing variables or add new variables to this file as you see fit.
- The **env.bat** file sets needed environment variables when working in the Toontown Retro environment, similar to .bashrc. One important environment variable is ``%PLAYER%``, which is set to the location of your player directory. You can add extra variables and commands to this file as you see fit.
- The **Terminal** shortcut launches a console window that automatically pulls in the commands and environment variables from env.bat when you launch it.

#### Configuring env.bat and Config.pp
The **env.bat** file is the Batch script that automatically executes when you open the console using the Terminal shortcut. This file requires modifications to reference the correct paths to the Python and Maya installations on your computer. Open this file in a text editor, and correct the lines reading ``set PYTHON_LOCATION=`` and ``set MAYA_LOCATION=`` to the correct paths on your computer. Additionally, you may have to correct the location to the Visual Studio vcvars script, which sets up appropriate environment variables to invoke the Visual C++ compiler.

If you wish to use Clang as default compiler over the Microsoft Visual Studio one you will need to edit Config.pp and add the following line for msbuild/jom to respect that setting: 
``#define USE_COMPILER Clang``

Close your current console, and from now on, **launch the console using the Terminal shortcut.**

#### Building WINTOOLS
This builds all of the third-party packages required to build the actual engine. **Note: You should now be using the Terminal console.**

First, you should attach to WINTOOLS. This adds appropriate variables to your environment that reference folders within WINTOOLS, such as the location of the built third-party packages.
```
cta wintools
```
Attaching to WINTOOLS (or any other tree for that matter) also sets an environment variable with the name of the tree that references the location of the tree. For instance, after attaching, ``%WINTOOLS%`` will be defined as ``C:\Users\%username%\player\wintools``. 

Attaching to trees also adds the name of that tree onto another environment variable called ``%CTPROJS%``, which contains a list of all the trees you are attached to. In this case, ``echo %CTPROJS%`` will print ``WINTOOLS:default``. If you are attached to more than one tree, the tree names will be separated with a ``+`` symbol.

There is another command called ``ctshowprojs`` that you can run that will list your current attachments with nicer formatting than directly printing ``%CTPROJS%``.

It is also convenient to place all of your attachment commands in ``env.bat``, so launching the Terminal automatically attaches to the trees you need. It's recommended you do that.

Now, you want to enter the WINTOOLS directory and build the third-party packages:
```
cd %WINTOOLS%
mkdir build && cd build
cmake .. -G "Visual Studio 16 2019" -A x64
cmake --build . -j --config RelWithDebInfo
```
It will take some time to build, but once it is done, all of the third-party packages will have been built and installed into ``%WINTOOLS%\built``.

#### Copying WINTOOLS DLLs
All of the DLLs built by WINTOOLS are expected to be under a single directory, but the build process puts the DLLs for each third-party package in its own directory.

After WINTOOLS is done building, run the following commands:
```
cd %WINTOOLS%
python copy_dlls.py
```

#### Adding 3rd Party Libraries
Projects have custom dependencies on 3rd party libraries that are required to have installed beforehand.

For the **FMOD Sound System**, the ``fmod.dll`` file has to be copied from ``C:\Program Files (x86)\FMOD SoundSystem\Fmod Studio API Windows\api\core\lib\x64`` into the ``%WINTOOLS%\built\bin`` folder.
  - Toontown depends on this

**Steam Audio** is depended on by both Toontown and Team Fortress.
- Copy the DLLs from ``C:\Users\%Username%\steamaudio\lib\windows-x64`` into ``wintools/built/bin``.
- In your local Config.pp file, add the following lines:
```
#define STEAM_AUDIO_IPATH $[USERPROFILE]\steamaudio\include
#define STEAM_AUDIO_LPATH $[USERPROFILE]\steamaudio\lib\windows-x64
```

For **Autodesk Maya**, configure ``env.bat`` to ensure that the path to Maya (``MAYA_LOCATION``) is correct.
Example:
```
set MAYA_LOCATION=C:\Program Files\Autodesk\Maya2016
```
Afterwards, install the DevKit by extracting the contents into your ``MAYA_LOCATION`` directory.

#### Building ppremake
The rest of the trees rely on the ppremake build system. Before you build the engine, you will need to clone and build ppremake:
```
cd %PLAYER%
git clone [https://github.com/toontownretro/ppremake](https://github.com/toontownretro/ppremake)
```

After ppremake has been cloned, attach to it and build it:
```
cta ppremake
cd %PPREMAKE%
msbuild ppremake.sln -p:Configuration=Release;Platform=x64
```
The output of this is an executable named ``ppremake.exe``, found in ``%PPREMAKE%\built\bin``.

#### Building DTOOL
DTOOL is the tree that contains low-level code that is needed by the rest of the engine and game trees. Building DTOOL requires for the desired third-party packages to be installed and configured beforehand.

First, clone the DTOOL repository:
```
cd %USERPROFILE%\player
git clone https://github.com/toontownretro/dtool
```

Attach to DTOOL, just like you did for WINTOOLS and ppremake:
```
cta dtool
```

Enter the DTOOL directory and let ppremake generate the scripts to build the tree:
```
cd %DTOOL%
ppremake
```

Before building, ensure that your config looks correct:
![](https://i.imgur.com/Jm5YdVl.png)

If your config is missing a dependency such as Autodesk Maya, view the FAQ [here](https://github.com/toontownretro/documentation/blob/main/docs/getting_started/setup_dtool_faq.md).


Finally, build the tree:
```
msbuild dtool.sln -m -t:install
```
*or*
```
jom install
```

#### Building PANDA
This tree contains the main part of the engine (graphics, audio, networking, etc). It relies on DTOOL.

Preparing PANDA:
```
cd %PLAYER%
git clone https://github.com/toontownretro/panda
cta panda
cd %PANDA%
ppremake
```

Building PANDA:
```
msbuild panda.sln -m -t:install
```
*or*
```
jom install
```

After this completes, you should now have a fully built PANDA tree in ``%PANDA%\built``.

#### Building PANDATOOL
This tree contains various command line programs for use with PANDA, such as pview, maya2egg, etc.

Preparing PANDATOOL:
```
cd %PLAYER%
git clone https://github.com/toontownretro/pandatool
cta pandatool
cd %PANDATOOL%
ppremake
```

Building PANDATOOL:
```
msbuild pandatool.sln -m -t:install
```
*or*
```
jom install
```

After this completes, you should now have a fully built PANDATOOL tree in ``%PANDATOOL%\built``. You can verify this by running pview from the command line.

#### Building DIRECT
This tree is a high-level Python interface built on top of PANDA. It contains the code for Actors, DistributedObjects, ShowBase, etc.

Preparing DIRECT:
```
cd %PLAYER%
git clone https://github.com/toontownretro/direct
cta direct
cd %DIRECT%
ppremake
```

Building DIRECT:
```
msbuild direct.sln -m -t:install
```
*or*
```
jom install
```

#### Building DMODELS
This tree contains various assets used by DIRECT:
```
cd %PLAYER%
git clone https://github.com/toontownretro/dmodels
cta dmodels
cd %DMODELS%
ppremake
nmake install
```
Note the use of nmake instead of msbuild. msbuild is used to build code trees, while nmake is used to build model/asset trees.

Optionally, rather than nmake install to build DMODELS, you can enter nmake opt-pal. This will use the egg-palettize program in PANDATOOL to place lots of individual textures onto a smaller number of larger textures called palettes, which improves rendering performance. This also applies to TTMODELS.

---

#### Building OTP
This tree contains code that is shared between Toontown and Pirates, such as the code for name tags.

Preparing OTP:
```
cd %PLAYER%
git clone https://github.com/toontownretro/otp
cta otp
cd %OTP%
ppremake
```

Preparing OTP:
```
msbuild otp.sln -m -t:install
```
*or*
```
jom install
```

#### [Building TOONTOWN](https://github.com/toontownretro/documentation/blob/main/docs/getting_started/setup_toontown.md)


#### Optional: Building the OTP Server
This tree contains the code for the OTP server in which the client, AI and UD all connect too.

It's a requirement to run and play the game if you're not using Astron.
```
cd %PLAYER%
git clone https://github.com/toontownretro/otp_server
cta otp_server
cd %OTP_SERVER%
ppremake
msbuild otp_server.sln -m -t:install
```

#### Running TOONTOWN and OTP

After all previous steps were completed the TOONTOWN and OTP trees are ready to be used for running the game.

Open four consoles using the Terminal shortcut and use the following commands, one for each Terminal.

If you're using Astron, Enter this command into the first terminal.
```
cd astron & astrond config/astrond.yml
```
Otherwise enter this command into the first terminal.
```
%PYTHON_LOCATION%\python.exe -m otp_server.realtime.main
```
Then after starting either Astron as your OTP or the custom OTP we provide, enter these three commands into the three other terminals you started.
```
%PYTHON_LOCATION%\python.exe -m toontown.ai.AIStart
%PYTHON_LOCATION%\python.exe -m toontown.uberdog.Start
%PYTHON_LOCATION%\python.exe -m toontown.uberdog.UDStart
%PYTHON_LOCATION%\python.exe -m toontown.toonbase.ToontownStart
```
Keep in mind, the pure developer code was from mid 2010 shortly after Victory Parties ended and because of that a lot of the late game content is missing such as, Field Offices, Toon Accessories, M.A.P.S and the Skip Button, which will be added from time to time.

Additionally, a lot of the code requires small changes to be compatible with Python 3 which may take time to be updated.
