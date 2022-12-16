Don't forget to run the following before building (if needed):
```
git pull
ppremake
```

---

Required

| Name                         | Command                             |
|------------------------------|-------------------------------------|
| DIRECT                       | msbuild direct.sln -m -t:install    |
| DMODELS                      | nmake install                       |
| DTOOL                        | msbuild dtool.sln -m -t:install     |
| PANDA                        | msbuild panda.sln -m -t:install     |  
| PANDATOOL                    | msbuild pandatool.sln -m -t:install |
| PPREMAKE                     | msbuild ppremake.sln -p:Configuration=Release;Platform=x64     |
| WINTOOLS                     | cd build && cmake .. -G "Visual Studio 16 2019" -A x64 && cmake --build . -j --config RelWithDebInfo     |

---

Extensions

| Name                         | Command                             |
|------------------------------|-------------------------------------|
| [TOONTOWN](https://github.com/toontownretro/documentation/blob/main/docs/getting_started/setup_toontown.md#building-toontown) | msbuild toontown.sln -m -t:install |
| OTP | msbuild otp.sln -m -t:install |

