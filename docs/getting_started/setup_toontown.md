# TOONTOWN
This tree contains the actual Toontown game code, both client and server.

# Prerequisites
- [Finished installing Player components](https://github.com/toontownretro/documentation/blob/main/docs/getting_started/setup_player.md)
- [Finished installing the OTP package](https://github.com/toontownretro/documentation/blob/main/docs/getting_started/setup_otp.md)

# Required Tools
- Built Panda with FMOD, Steam Audio, and Maya tools
- Patience :)

# Required Dependencies
```
pip install pymysql
pip install wxPython
pip install pmw
pip install pytz
```

# Preparing TOONTOWN
```
cd %PLAYER%
git clone https://github.com/toontownretro/toontown
cta toontown
cd %TOONTOWN%
ppremake
```

# Building TOONTOWN:
```
msbuild toontown.sln -m -t:install
```
*or*
```
jom install
```

# Building TTMODELS
This tree contains the Toontown assets. **Note:** It can take hours to build from scratch (there's a lot of stuff).

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
As noted in the DMODELS step, you can optionally use ``nmake opt-pal`` rather than ``nmake install`` if you want to build palettes.

After TTMODELS has been built, you need to use a special script to remove the ``_language`` references so the game can find all files correctly.

Open the Terminal shortcut and use the following command:
```
cd %TTMODELS%\built & %PYTHON_LOCATION%\python.exe ..\src\strip_language.py
```
By default the script will ask to change all files ending with ``_english`` in their name. If you built TTMODELS in any other language than English you will need to manually write the language you built it in the Terminal.

Supported languages include:
- english
- castillian
- japanese
- german
- french
- portuguese

# Running TOONTOWN

After all previous steps were completed the TOONTOWN and OTP trees are ready to be used for running the game.

Open four consoles using the Terminal shortcut and use the following commands, one for each Terminal.

```
%PYTHON_LOCATION%\python.exe -m otp_server.py_otp
%PYTHON_LOCATION%\python.exe -m toontown.ai.AIStart
%PYTHON_LOCATION%\python.exe -m toontown.uberdog.Start
%PYTHON_LOCATION%\python.exe -m toontown.toonbase.ToontownStart
```
