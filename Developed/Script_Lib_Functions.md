# GEN Script Libraries functions 

           
### Dir

```
IsItExists()
ChangeDir() 
```             

### Math
```
Abs()
```

### Path
```
GetPathScript()
```

### Rand
```
RandMax()
RandBetween()
```

### String
```
AddString()
FindString()            # haystack, needle, ignorecase [, startindex]
CompareString()
ReplaceString()         # first occurrence (mutates first arg)
ReplaceAllString()      # all occurrences → new string
SPrintf()
GetStringSize()
IsEmptyString()
SubString()             # text, start, end  → [start, end)
SubStringFrom()         # text, start      → from start to end
ExtractBetween()        # text, startmark, endmark [, ignorecase [, from]]
TrimString()
ToUpperString()
ToLowerString()
GetCharString()         # text, index → one-char string
```

### WebClient
```
WebClient_Get()               # url [, headers [, timeout]] → bool
WebClient_Post()              # url, body [, headers [, timeout]] → bool
WebClient_GetToFile()         # url, path [, headers [, timeout]] → bool
WebClient_GetBody()           # last response body as string
WebClient_GetStatus()         # HTTP status code
WebClient_GetHeader()         # name → response header value
WebClient_GetLastError()      # DIOWEBCLIENT_ERROR code
WebClient_SetLogin()          # user, password → bool
WebClient_DoStopHTTPError()   # activate → bool
```

### Timer
```
Sleep()
```

### System
```
System_GetType()                    # → Windows | Linux | LinuxEmbedded | Android | STM32 | ESP32 | SAMD5xE5x
System_GetOperativeSystemID()       # → detailed OS ID string
System_GetHardwareType()            # → PC | RaspberryPi | Unknown | …
System_IsWindows()                  # → bool
System_IsLinux()                    # → bool (Linux + LinuxEmbedded)
System_IsAndroid()                  # → bool
System_GetLanguageSO()              # → language code (int)
System_GetUser()                    # → current user
System_GetDomain()                  # → current domain
System_GetFreeMemoryPercent()       # → 0..100
System_GetPathExecApplication()     # appname → absolute path (or empty)
System_Reboot()
System_PowerOff()
System_Logout()
System_GetEnviromentVar()           # varname → value
```

### Process
```
OpenURL()
ExecApplication()
MakeCommand()
TerminateAplication()
TerminateAplicationWithWindow()
```

### Log
```
Log_AddEntry()
XTRACE_PRINTCOLOR()
```

### Console
```
Console_GetChar()
Console_PutChar()
Console_Printf()
```

### CFG
```
GetFileCFGValue()
```
 
### Screen
```
Screen_GetPosX()     # app, title [, bmp...], out_x → status (0=OK,1=NOTFOUND,2=BMPNOTFOUND); writes out_x
Screen_GetPosY()     # app, title [, bmp...], out_y → status; writes out_y
Screen_GetPosXY()    # app, title [, bmp...], out_x, out_y → status; writes out_x/out_y
                     # JS/Lua out boxes: {value:0} (also mirrored to [0]/[1]); G: plain int variables
Screen_SetBmpFindCFG()
Screen_SetFocus()
Screen_SetPosition()
Screen_Resize()
Screen_Minimize()
Screen_Maximize()
```

### InputSimulate 
```

InpSim_Key_Press()
InpSim_Key_UnPress()
InpSim_Key_Click()
InpSim_Key_ClickByLiteral()
InpSim_Key_ClickByText()
InpSim_Mouse_SetPos()
InpSim_Mouse_Click()
      
```


