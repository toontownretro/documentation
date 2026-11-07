#### Building the OTP Server
This tree contains the code for the OTP server in which the client, AI and UD all connect to.

# Prerequisites
- [Finished installing Player components](https://github.com/toontownretro/documentation/blob/main/docs/getting_started/setup_player.md)

# Python OTP SERVER:

## Required Dependencies
```
pip install pycryptodome
pip install pytz
pip install semidbm
pip install pymysql
```

# C++ OTP SERVER:

### Preparing the OTP Server
```
cd %PLAYER%
git clone https://github.com/toontownretro/otp_server
cta otp_server
cd %otp_server%
ppremake
```

### Building the OTP Server:
```
msbuild otp_server.sln -m -t:install
```
*or*
```
jom install
```
