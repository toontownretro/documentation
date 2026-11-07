# OTP
This tree contains code that is shared between the VR Studio MMOs.

# Prerequisites
- [Finished installing Player components](https://github.com/toontownretro/documentation/blob/main/docs/getting_started/setup_player.md)

# Required Dependencies
```
pip install pymysql
pip install cherrypy
pip install wxPython
```

# Preparing OTP:
```
cd %PLAYER%
git clone https://github.com/toontownretro/otp
cta otp
cd %OTP%
ppremake
```

# Building OTP:
```
msbuild otp.sln -m -t:install
```
*or*
```
jom install
```
