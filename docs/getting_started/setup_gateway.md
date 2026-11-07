# GATEWAY
This tree contains the actual Gateway code.

# DISCLAIMER:
Currently there is no way to tell how finished Gateway was at the time of its cancelation and potential succession by DXD Player Friends, old Panda3D commits and Anesidora reveal Gateway having both its own code and asset packages, so this guide should be taken more as a *what if* should either be revealed to the public.

# Prerequisites
- [Finished installing Player components](https://github.com/toontownretro/documentation/blob/main/docs/getting_started/setup_player.md)
- [Finished installing the OTP package](https://github.com/toontownretro/documentation/blob/main/docs/getting_started/setup_otp.md)

## Required Tools
- Built Panda with FMOD, Steam Audio, and Maya tools
- Patience :)

# Preparing Gateway
```
cd %PLAYER%
git clone https://github.com/toontownretro/gateway
cta gateway
cd %GATEWAY%
ppremake
```

Building GATEWAY:
```
msbuild gateway.sln -m -t:install
```
*or*
```
jom install
```

# Building GWMODELS
This tree contains the Gateway assets.

The GWMODELS build process relies on a client/server mechanism to convert models out of Maya. This is done to speed up the build process by not having to contact the Maya license server for each model that needs to be converted.

Open a second console using the Terminal shortcut and start the Maya conversion server:
```
cta wintools
cta dtool
cta panda
cta pandatool
maya2egg_server
```

Now, back in the first console, clone and start the GWMODELS build:
```
cd %PLAYER%
git clone https://github.com/toontownretro/gwmodels
cta gwmodels
cd %GWMODELS%
ppremake
nmake install
```
As noted in the DMODELS step, you can optionally use ``nmake opt-pal`` rather than ``nmake install`` if you want to build palettes.
