# PIRATES
This tree contains the actual Pirates game code, both client and server.

# DISCLAIMER:
Currently there are no plans for supporting Pirates, [another open-source project aiming to restore Pirates Online can be found here](https://gitlab.com/sunrisemmos/pirates/game).

# Prerequisites
- [Finished installing Player components](https://github.com/toontownretro/documentation/blob/main/docs/getting_started/setup_player.md)
- [Finished installing the OTP package](https://github.com/toontownretro/documentation/blob/main/docs/getting_started/setup_otp.md)

# Required Tools
- Built Panda with FMOD, Steam Audio, and Maya tools
- Patience :)

# Required Dependencies
```
pip install pymysql
pip install pmw
pip install wxPython
```

# Preparing PIRATES
```
cd %PLAYER%
git clone https://github.com/toontownretro/pirates
cta pirates
cd %PIRATES%
ppremake
```

# Building PIRATES:
```
msbuild pirates.sln -m -t:install
```
*or*
```
jom install
```

# Building PMODELS/PMOCKUP
This tree contains the Pirates assets. **Note:** It can take hours to build from scratch (there's a lot of stuff).

The PMODELS/PMOCKUP build process relies on a client/server mechanism to convert models out of Maya. This is done to speed up the build process by not having to contact the Maya license server for each model that needs to be converted.

Open a second console using the Terminal shortcut and start the Maya conversion server:
```
cta wintools
cta dtool
cta panda
cta pandatool
maya2egg_server
```

Now, back in the first console, clone and start the PMODELS/PMOCKUP build:
```
cd %PLAYER%
git clone https://github.com/toontownretro/pmodels or git clone https://github.com/toontownretro/pmockup
cta pmodels or cta pmockup
cd %PMODELS% or cd %PMOCKUP%
ppremake
nmake install
```
As noted in the DMODELS step, you can optionally use ``nmake opt-pal`` rather than ``nmake install`` if you want to build palettes.

# Running PIRATES

After all previous steps were completed the PIRATES and OTP trees are ready to be used for running the game.

Open four consoles using the Terminal shortcut and use the following commands, one for each Terminal.

```
%PYTHON_LOCATION%\python.exe -m otp_server.py_otp
%PYTHON_LOCATION%\python.exe -m pirates.ai.AIStart
%PYTHON_LOCATION%\python.exe -m pirates.uberdog.Start
%PYTHON_LOCATION%\python.exe -m pirates.piratesbase.PiratesStart
```
