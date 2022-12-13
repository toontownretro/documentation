# Prerequisites
- finished building otp module


## Required Tools
- MySQL
- Built Panda with FMOD, Steam audio, and maya support

# Building TOONTOWN
This tree contains the actual Toontown game code, both client and server.
```
cd %PLAYER%
git clone https://github.com/toontownretro/toontown
cta toontown
cd %TOONTOWN%
ppremake
msbuild toontown.sln -m -t:install
```

# Building TTMODELS
This tree contains the Toontown assets. It can take hours to build from scratch (there's a lot of stuff).

The TTMODELS build process relies on a client/server mechanism to convert models out of Maya. This is done to speed up the build process by not having to contact the Maya license server for each model that needs to be converted.

Open a second console using the Terminal shortcut and start the Maya conversion server:
```
cta wintools
cta dtool
cta panda
cta pandatool
maya2egg_server
```

Now, back in the first console, clone and start the TTMODELS build:
```
cd %PLAYER%
git clone https://github.com/toontownretro/ttmodels
cta ttmodels
cd %TTMODELS%
ppremake
nmake install
```
As noted in the DMODELS step, you can optionally use nmake opt-pal rather than nmake install if you want to build palettes.

After TTMODELS has been built, you need to use a special script to remove the ``_language`` references so the game can find all files correctly.

Open the Terminal shortcut and use the following command:
```
cd %TTMODELS%\built & %PYTHON_LOCATION%\python.exe ..\src\strip_language.py
```
By default the script will ask to change all files ending with ``_english`` in their name. If you built TTMODELS in any other language than English you will need to manually write the language you built it in the Terminal.