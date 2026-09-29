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
System_GetType()
System_Reboot()
System_PowerOff()
System_Logout()
System_GetEnviromentVar()
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
 
### Windows
```
Window_GetPosX()
Window_GetPosY()
Window_SetFocus()
Window_SetPosition()
Window_Resize()
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


