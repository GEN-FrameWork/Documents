# Traducción al Español (ES)

# GEN C++ Code Style Guide for New Modules

**Purpose:** this document defines the code style rules to follow when writing a new GEN module so that the new code looks, reads, and behaves like the existing GEN codebase.

**Scope analyzed:** the supplied GEN source tree, with particular attention to `.h` and `.cpp` files across `AppFlow`, `XUtils`, `DataIO`, `Graphic`, `Cipher`, `UserInterface`, `Platforms`, and related modules. The rules below are descriptive: they document the style already used by GEN, not a generic modern C++ style.

**Non-negotiable principle:** when this guide conflicts with a common external C++ convention, follow GEN. GEN uses its own vocabulary, naming, lifecycle, alignment, documentation blocks, macros, type aliases, and object-management patterns.

---

## 1. Global Style Summary

A new GEN module must follow these visible characteristics:

1. Use one header `.h` and one implementation `.cpp` per main class.
2. Use the GEN file banner at the top of every source file.
3. Use `#pragma once` in headers.
4. Use explicit section separators such as `/*---- INCLUDES ----*/`, `/*---- DEFINES & ENUMS ----*/`, `/*---- CLASS ----*/`, `/*---- CLASS MEMBERS ----*/`.
5. Use uppercase class names, usually with a module prefix: `XSTRING`, `DIOPROTOCOL`, `APPFLOWALERTS`, `GRP2DCANVAS`, `CIPHERKEY`.
6. Use uppercase enum type names and uppercase enum values prefixed by the enum/module name.
7. Use uppercase `#define` names, usually prefixed by the module or framework namespace.
8. Use GEN primitive types (`XBYTE`, `XWORD`, `XDWORD`, `XQWORD`, `XCHAR`, `XSTRING`, `XPATH`, `XBUFFER`) instead of raw standard types where the existing framework has an equivalent.
9. Use `NULL`, `TRUE`, `FALSE`, `NOTFOUND`, and GEN macros where the local codebase does so.
10. Use `Ini()` / `End()` / `Clean()` lifecycle methods instead of generic `Init()` / `Shutdown()` / `ResetMembers()` names.
11. Use aligned declarations in classes and function prototypes.
12. Use two-space indentation for block nesting.
13. Put braces on their own line for functions and multi-line control blocks.
14. Prefer compact early returns such as `if(!ptr) return false;` where already common.
15. Document public and implemented functions with GEN Doxygen blocks.
16. Keep method order in `.cpp` aligned with declaration order in `.h`.
17. Place `GEN_Defines.h` first in `.cpp` files, and `GEN_Control.h` near the end of the include section.
18. Use `GEN_NEW` for heap allocation where the codebase uses framework allocation macros.
19. Use forward declarations in headers to reduce includes.
20. Maintain the exact visual alignment and spacing conventions even when they appear unusual.

---

## 2. File Naming Rules

### 2.1 Header and Source Pair

For a main class named `MYMODULE`, create:

```text
MyModule.h
MyModule.cpp
```

GEN does not require the filename to be fully uppercase. Existing files commonly use Pascal-like filenames with module prefixes:

```text
APPFlowAlerts.h
APPFlowAlerts.cpp
DIOProtocol.h
DIOProtocol.cpp
XString.h
XString.cpp
UI_Manager.h
UI_Manager.cpp
```

Use the spelling style of the folder you are extending. For example:

- `APPFlowSomething.h` in `AppFlow`.
- `DIOProtocolSomething.h` in `DataIO/Protocols/...`.
- `XSomething.h` in `XUtils`.
- `GRPSomething.h` in `Graphic`.
- `UI_Something.h` in `UserInterface`.

### 2.2 Event Files

Event classes use `_XEvent` in filenames and `_XEVENT` in class names:

```text
APPFlow_XEvent.h          -> APPFLOW_XEVENT
DIOCamera_XEvent.h        -> DIOCAMERA_XEVENT
APPFlowUpdate_XEvent.h    -> APPFLOWUPDATE_XEVENT
```

Do not name event files `Event`, `Events`, `Xevent`, or `xevent`. Use exactly `_XEvent` in the filename and `_XEVENT` in the class.

### 2.3 Platform Files

Platform-specific code is placed below `Platforms/<PlatformName>/...` and the filename/class should include the platform prefix when it specializes a generic concept.

Examples of expected platform concepts:

```text
XWINDOWS...
XLINUX...
XANDROID...
XSTM32...
XESP32...
```

---

## 3. Header File Layout

A header must use this order:

```cpp
/**-------------------------------------------------------------------------------------------------------------------
* 
* @file       MYModule.h
* 
* @class      MYMODULE
* @brief      My Module class
* @ingroup    MYGROUP
* 
* @copyright  EndoraSoft. All rights reserved.
* 
* @cond
* Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated
* documentation files(the "Software"), to deal in the Software without restriction, including without limitation
* the rights to use, copy, modify, merge, publish, distribute, sublicense, and/ or sell copies of the Software,
* and to permit persons to whom the Software is furnished to do so, subject to the following conditions:
* 
* The above copyright notice and this permission notice shall be included in all copies or substantial portions of
* the Software.
* 
* THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO
* THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.IN NO EVENT SHALL THE
* AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
* TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
* SOFTWARE.
* @endcond
* 
* --------------------------------------------------------------------------------------------------------------------*/

#pragma once

/*---- INCLUDES ------------------------------------------------------------------------------------------------------*/

#include "XBase.h"
#include "XString.h"



/*---- DEFINES & ENUMS  ----------------------------------------------------------------------------------------------*/

#define MYMODULE_DEFAULT_VALUE          10


enum MYMODULE_STATUS
{
  MYMODULE_STATUS_UNKNOWN              = 0 ,
  MYMODULE_STATUS_ACTIVE                   ,
  MYMODULE_STATUS_ERROR                    ,
};



/*---- CLASS ---------------------------------------------------------------------------------------------------------*/

class XBUFFER;
class XTIMER;

class MYMODULE
{
  public:

                                      MYMODULE                         ();
    virtual                          ~MYMODULE                         ();

    bool                              Ini                              ();
    bool                              End                              ();

    XSTRING*                          GetName                          ();
    void                              SetName                          (XCHAR* name);

  private:

    void                              Clean                            ();

    XSTRING                           name;
    bool                              isactive;
};



/*---- INLINE FUNCTIONS + PROTOTYPES ---------------------------------------------------------------------------------*/
```

Required details:

- There is one blank line after the banner before `#pragma once`.
- Section headers use `/*---- ... ----*/` with a long dash fill.
- Keep two blank lines between major sections.
- Use forward declarations after defines/enums and before the main class.
- Put the class declaration after `/*---- CLASS ----*/`.
- Leave the `/*---- INLINE FUNCTIONS + PROTOTYPES ----*/` section even when empty.

---

## 4. Source File Layout

A `.cpp` file must use this order:

```cpp
/**-------------------------------------------------------------------------------------------------------------------
* 
* @file       MYModule.cpp
* 
* @class      MYMODULE
* @brief      My Module class
* @ingroup    MYGROUP
* 
* @copyright  EndoraSoft. All rights reserved.
* 
* @cond
* ... same license text ...
* @endcond
* 
* --------------------------------------------------------------------------------------------------------------------*/

/*---- PRECOMPILATION INCLUDES ---------------------------------------------------------------------------------------*/

#include "GEN_Defines.h"



/*---- INCLUDES ------------------------------------------------------------------------------------------------------*/

#include "MYModule.h"

#include "XBuffer.h"
#include "XTrace.h"



/*---- PRECOMPILATION INCLUDES ---------------------------------------------------------------------------------------*/

#include "GEN_Control.h"




/*---- GENERAL VARIABLE ----------------------------------------------------------------------------------------------*/

MYMODULE* MYMODULE::instance = NULL;



/*---- CLASS MEMBERS -------------------------------------------------------------------------------------------------*/
```

Rules:

1. Include `GEN_Defines.h` first in `.cpp` files when the module participates in the framework.
2. Include the matching header before secondary module headers.
3. Group includes logically with blank lines, not alphabetically at all costs.
4. Include `GEN_Control.h` after normal includes and before globals/class members.
5. Put singleton static instance definitions in `/*---- GENERAL VARIABLE ----*/`.
6. Put function implementations under `/*---- CLASS MEMBERS ----*/`.
7. Use the same file banner format as the header, with `@file` changed to `.cpp`.

---

## 5. Section Separator Format

GEN uses prominent visual separators. Use these exact labels where applicable:

```cpp
/*---- PRECOMPILATION INCLUDES ---------------------------------------------------------------------------------------*/
/*---- INCLUDES ------------------------------------------------------------------------------------------------------*/
/*---- DEFINES & ENUMS  ----------------------------------------------------------------------------------------------*/
/*---- CLASS ---------------------------------------------------------------------------------------------------------*/
/*---- GENERAL VARIABLE ----------------------------------------------------------------------------------------------*/
/*---- CLASS MEMBERS -------------------------------------------------------------------------------------------------*/
/*---- INLINE FUNCTIONS + PROTOTYPES ---------------------------------------------------------------------------------*/
```

Additional local separators are allowed for long functions, especially comments like:

```cpp
//-----------------------------------------------------------------------------------------
// SMTP
```

Do not replace section separators with `// Includes`, `// Definitions`, `namespace`, `#region`, or decorative styles from other projects.

---

## 6. Indentation and Whitespace

### 6.1 Indentation Unit

Use **two spaces** for indentation. Do not use tabs.

Correct:

```cpp
if(isactive)
  {
    value = 1;
  }
```

Incorrect:

```cpp
if(isactive) {
    value = 1;
}
```

### 6.2 Brace Placement

Function braces are on their own lines:

```cpp
bool MYMODULE::Ini()
{
  Clean();
  return true;
}
```

Multi-line control-block braces are also on their own lines:

```cpp
if(instance)
  {
    delete instance;
    instance = NULL;

    return true;
  }
```

For simple early-return guards, a one-line statement is common and acceptable:

```cpp
if(!cfg) return false;
if(!stream) return false;
```

Do not force braces around every one-line guard if the surrounding GEN file uses compact guards.

### 6.3 Blank Lines

Use blank lines to create vertical rhythm:

- Two blank lines after include groups.
- Two blank lines before major section separators.
- One blank line between logically different blocks inside a function.
- One blank line after setting an object to `NULL` before returning, where the existing pattern does so.

Example:

```cpp
if(instance)
  {
    delete instance;
    instance = NULL;

    return true;
  }

return false;
```

### 6.4 Alignment Spaces

GEN frequently aligns identifiers, return types, method names, parameters, enum values, and macro values using spaces.

Preserve this style. Do not collapse alignment just because a formatter would do so.

Correct class declaration style:

```cpp
class MYMODULE
{
  public:

    bool                              Ini                              ();
    bool                              End                              ();

    XSTRING*                          GetName                          ();
    void                              SetName                          (XCHAR* name);

  private:

    void                              Clean                            ();

    XSTRING                           name;
    bool                              isactive;
};
```

Correct enum alignment:

```cpp
enum MYMODULE_STATUS
{
  MYMODULE_STATUS_UNKNOWN             = 0 ,
  MYMODULE_STATUS_ACTIVE                  ,
  MYMODULE_STATUS_ERROR                   ,
};
```

Correct macro alignment:

```cpp
#define MYMODULE_TIMEOUT              5
#define MYMODULE_MAXBUFFER            1024
#define MYMODULE_DEFAULT_NAME         __L("module")
```

### 6.5 Spaces Around Operators and Punctuation

GEN style is mixed but follows several strong patterns:

- No space after unary `!`: `if(!instance)`.
- No mandatory spaces around `=` inside compact loop headers: `for(int c=0; c<size; c++)` is common.
- Spaces are often used for visual alignment around assignments in repeated blocks.
- Function calls do not put a space between function name and `(`.
- In declarations, GEN often aligns the function name and then writes parameter parentheses in a separate visual column.

Examples:

```cpp
for(int c=0; c<list.GetSize(); c++)
  {
    value[c] = 0;
  }

status[c]      = false;
nrecipients[c] = 0;
```

Do not reformat aligned blocks into a compact external style.

---

## 7. Naming Conventions

### 7.1 Module Prefixes

Names must carry their module prefix. Common prefixes include:

| Area | Prefix examples |
|---|---|
| XUtils | `X`, `XFILE`, `XSTRING`, `XBUFFER`, `XTIMER` |
| DataIO | `DIO`, `DIOSTREAM`, `DIOPROTOCOL` |
| AppFlow | `APPFLOW`, `APPFLOWCFG`, `APPFLOWALERTS` |
| Graphic | `GRP`, `GRP2DCANVAS`, `GRPBITMAP` |
| User Interface | `UI`, `UI_MANAGER`, `UI_ELEMENT` |
| Cipher | `CIPHER`, `HASH`, `CIPHERAES` |
| Script | `SCRIPT`, `SCRIPT_LIB` |
| Input | `INP`, `INPDEVICE` |
| Sound | `SND` or `SOUND` according to the local folder |
| Platform Windows | `XWINDOWS`, `DIOWINDOWS`, `GRPWINDOWS` |
| Platform Linux | `XLINUX`, `DIOLINUX`, `GRPLINUX` |
| Platform Android | `XANDROID`, `DIOANDROID`, `GRPANDROID` |
| Microcontroller | `XSTM32`, `XESP32`, `DIOSTM32`, `DIOESP32` |

A new module must use the prefix of its subsystem. Do not introduce an unrelated prefix.

### 7.2 Class Names

Class names are uppercase and normally concatenated without underscores except for specialized suffixes such as `_XEVENT`.

Correct:

```cpp
class DIOPROTOCOL;
class APPFLOWALERTS;
class XSTRING;
class GRP2DCANVAS;
class MYMODULE;
class MYMODULE_XEVENT;
```

Incorrect:

```cpp
class DioProtocol;
class AppFlowAlerts;
class xstring;
class MyModuleEvent;
```

### 7.3 Method Names

Public methods use PascalCase or GEN lifecycle names:

```cpp
Ini()
End()
Clean()
GetName()
SetName()
IsActive()
SetIsActive()
GetType()
SetType()
AddCommand()
DeleteAllAnswers()
ReceivedHandle()
```

Rules:

- Use `Get...` for getters.
- Use `Set...` for setters.
- Use `Is...` for boolean state queries.
- Use `Ini()` for initialization, not `Init()`.
- Use `End()` for shutdown/destruction-like finalization.
- Use `Clean()` for resetting member variables to defaults.
- Use `Delete...` for removal operations, not `Remove...`, unless extending a class that already uses `Remove`.
- Use existing GEN domain words even if misspelled or nonstandard, because API consistency is more important than external spelling.

### 7.4 Member Variable Names

Private member variables use lowercase or compact lowercase words:

```cpp
bool                              isactive;
XDWORD                            type;
XSTRING                           description;
void*                             applicationdata;
DIOSTREAM*                        diostream;
```

When the variable represents a GEN acronym, keep the local style:

```cpp
XDWORD                            ID;
XDWORD                            maskID;
XDWORD                            crc32;
```

Do not use `m_`, trailing underscores, or camelCase if the local class does not use them.

Incorrect:

```cpp
bool                              m_isActive;
XSTRING                           description_;
DIOSTREAM*                        dioStream;
```

### 7.5 Local Variables

Local variables are usually lowercase or compact lowercase. Short loop counters commonly use `c`.

```cpp
int  nrecipients[MYMODULE_TYPE_MAX];
bool status;

for(int c=0; c<items.GetSize(); c++)
  {
    ...
  }
```

GEN often uses concise local variable names. Do not replace every local variable with long descriptive modern names if the surrounding code is compact.

### 7.6 Constants and Defines

`#define` names are uppercase with underscores and a subsystem prefix:

```cpp
#define DIOPROTOCOL_TIMEOUT             5
#define DIOPROTOCOL_MAXBUFFER           (DIOSTREAM_MAXBUFFER/2)
#define APPFLOW_ALERTS_WEBALERTCMD      __L("alert")
```

Do not use unprefixed constants in public headers.

Incorrect:

```cpp
#define TIMEOUT 5
#define MaxBuffer 1024
```

### 7.7 Enum Types and Values

Enum type names are uppercase and prefixed by the owning class/module. Enum values repeat the enum/module prefix.

```cpp
enum MYMODULE_RESULT
{
  MYMODULE_RESULT_OK                    = 0 ,
  MYMODULE_RESULT_NOTMEM                    ,
  MYMODULE_RESULT_ERROR                     ,
};
```

For `MAX` sentinel values, use the same prefix:

```cpp
MYMODULE_TYPE_MAX
```

Do not use scoped enums (`enum class`) in GEN-style modules unless the surrounding subsystem already uses them. Existing GEN style uses plain `enum`.

### 7.8 Typedefs and Function Pointers

Function pointer typedefs use uppercase names and align parameters:

```cpp
typedef int (*MYMODULE_RECEIVEDFUNC)   (MYMODULE* module, XBUFFER& buffer, XDWORD& param);
```

Use this style instead of `using` aliases when matching existing GEN code.

---

## 8. Class Declaration Style

### 8.1 Access Order

Use this order:

```cpp
class MYMODULE
{
  public:

    ... public constructors/destructor ...
    ... public methods ...

  protected:

    ... protected methods/members if needed ...

  private:

    ... private constructors/copy prevention if singleton ...
    ... private methods ...
    ... static members ...
    ... data members ...
};
```

Many GEN classes omit `protected:` when not needed.

### 8.2 Constructor and Destructor Formatting

Constructors and destructors are aligned like methods. Destructors are commonly virtual when the class is intended for inheritance or follows the existing pattern:

```cpp
                                      MYMODULE                         ();
    virtual                          ~MYMODULE                         ();
```

For classes not intended to be polymorphic, existing code may use:

```cpp
                                      MYMODULE_ITEM                    ();
                                     ~MYMODULE_ITEM                    ();
```

Match the local pattern.

### 8.3 Method Declaration Alignment

Use column alignment:

```cpp
    bool                              AddItem                          (XDWORD type, XCHAR* name);
    MYMODULE_ITEM*                    GetItem                          (XDWORD index);
    bool                              DeleteItem                       (XDWORD index);
    bool                              DeleteAllItems                   ();
```

The return type column, method name column, and parameter-list column should be visually aligned.

### 8.4 Private `Clean()`

Most classes define a private `Clean()` method. It must reset every member to its default value.

Declaration:

```cpp
  private:

    void                              Clean                            ();
```

Implementation:

```cpp
void MYMODULE::Clean()
{
  isactive = false;
  type     = MYMODULE_TYPE_UNKNOWN;
  stream   = NULL;
}
```

Call `Clean()` from constructors and after releasing resources in destructors or `End()` where appropriate.

### 8.5 Copy Prevention for Singletons

Singleton classes use a private copy constructor and assignment operator with `// Don't implement` comments:

```cpp
  private:
                                      MYMODULE                         ();
                                      MYMODULE                         (MYMODULE const&);        // Don't implement
    virtual                          ~MYMODULE                         ();

    void                              operator =                       (MYMODULE const&);        // Don't implement
```

Do not use `= delete` if the surrounding class uses the older GEN idiom.

---

## 9. Singleton Pattern

GEN singletons follow a consistent pattern.

### 9.1 Header Pattern

```cpp
#define MYMODULE_MANAGER              MYMODULEMANAGER::GetInstance()

class MYMODULEMANAGER
{
  public:

    static bool                       GetIsInstanced                   ();
    static MYMODULEMANAGER&           GetInstance                      ();
    static bool                       DelInstance                      ();

    bool                              Ini                              ();
    bool                              End                              ();

  private:
                                      MYMODULEMANAGER                  ();
                                      MYMODULEMANAGER                  (MYMODULEMANAGER const&);  // Don't implement
    virtual                          ~MYMODULEMANAGER                  ();

    void                              operator =                       (MYMODULEMANAGER const&);  // Don't implement

    void                              Clean                            ();

    static MYMODULEMANAGER*           instance;
};
```

### 9.2 Source Pattern

```cpp
MYMODULEMANAGER* MYMODULEMANAGER::instance = NULL;


bool MYMODULEMANAGER::GetIsInstanced()
{
  return instance != NULL;
}


MYMODULEMANAGER& MYMODULEMANAGER::GetInstance()
{
  if(!instance) instance = GEN_NEW MYMODULEMANAGER();

  return (*instance);
}


bool MYMODULEMANAGER::DelInstance()
{
  if(instance)
    {
      delete instance;
      instance = NULL;

      return true;
    }

  return false;
}
```

Rules:

- Static member name is `instance`.
- Static member type is pointer to the class.
- Default value is `NULL`.
- Allocation uses `GEN_NEW` when existing module uses it.
- Delete uses `delete`, followed by `instance = NULL;`.
- Macro alias should call `Class::GetInstance()` and be prefixed.

---

## 10. Function Implementation Style

### 10.1 Doxygen Block Before Each Implemented Method

Implemented methods are documented with a large Doxygen block before the function.

Template:

```cpp
/**-------------------------------------------------------------------------------------------------------------------
* 
* @fn         bool MYMODULE::Ini(XCHAR* name, XDWORD type)
* @brief      Ini
* @ingroup    MYGROUP
* 
* @param[in]  name : 
* @param[in]  type : 
* 
* @return     bool : true if is succesful. 
* 
* --------------------------------------------------------------------------------------------------------------------*/
bool MYMODULE::Ini(XCHAR* name, XDWORD type)
{
  ...
}
```

Important details:

- Use `@fn` with the full signature.
- Use `@brief` with short GEN-style wording.
- Use `@ingroup` matching the file banner.
- Use `@param[in]`, `@param[out]`, or `@param[in/out]` with a colon.
- Use `@return     bool : true if is succesful.` for boolean functions where appropriate, preserving the existing spelling style if matching nearby files.
- Keep the separator line exactly in the same style.

### 10.2 Constructor Pattern

```cpp
MYMODULE::MYMODULE()
{
  Clean();
}
```

If the constructor receives dependencies, assign them after `Clean()` unless the local class pattern does otherwise:

```cpp
MYMODULE::MYMODULE(DIOSTREAM* diostream)
{
  Clean();

  this->diostream = diostream;
}
```

### 10.3 Destructor Pattern

```cpp
MYMODULE::~MYMODULE()
{
  End();

  Clean();
}
```

If no `End()` exists or no resources need release:

```cpp
MYMODULE::~MYMODULE()
{
  Clean();
}
```

### 10.4 `Ini()` Pattern

```cpp
bool MYMODULE::Ini()
{
  if(isinitialized) return true;

  isinitialized = true;

  return true;
}
```

Rules:

- Return `bool` for success/failure.
- Guard invalid parameters at the top.
- Use compact guards where common.
- Assign dependencies to `this->member` when parameter names match members.
- Avoid throwing exceptions.

### 10.5 `End()` Pattern

```cpp
bool MYMODULE::End()
{
  if(!isinitialized) return false;

  isinitialized = false;

  return true;
}
```

If deleting owned pointers:

```cpp
if(timer)
  {
    GEN_XFACTORY.DeleteTimer(timer);
    timer = NULL;
  }
```

Always reset pointers to `NULL` after deleting/freeing them.

### 10.6 `Clean()` Pattern

```cpp
void MYMODULE::Clean()
{
  isinitialized = false;
  isactive      = false;
  type          = MYMODULE_TYPE_UNKNOWN;
  stream        = NULL;
}
```

Rules:

- `Clean()` does not usually return a value.
- It should not allocate resources.
- It should set every member to a deterministic default.
- Align repeated assignments.

### 10.7 Getter Pattern

```cpp
XSTRING* MYMODULE::GetName()
{
  return &name;
}


XDWORD MYMODULE::GetType()
{
  return type;
}
```

Return pointers to internal GEN objects where the existing framework does so.

### 10.8 Setter Pattern

```cpp
void MYMODULE::SetType(XDWORD type)
{
  this->type = type;
}


bool MYMODULE::SetName(XCHAR* name)
{
  if(!name) return false;

  this->name.Set(name);

  return true;
}
```

Use `this->` when parameter names are identical to member names.

---

## 11. Control Flow Style

### 11.1 If Statements

Common forms:

```cpp
if(!pointer) return false;

if(condition)
  {
    DoSomething();
  }

if(condition)
       result = true;
  else result = false;
```

The aligned one-line `if/else` style appears in GEN. Use it sparingly and only when it improves consistency with nearby code.

### 11.2 For Loops

Typical style:

```cpp
for(int c=0; c<vector.GetSize(); c++)
  {
    item = vector.Get(c);
  }
```

Rules:

- Loop counter often named `c`.
- No mandatory spaces around `=` or `<` in the loop header.
- Braces on their own line for loop bodies.

### 11.3 Switch Statements

Use uppercase enum values and two-space indentation:

```cpp
switch(type)
  {
    case MYMODULE_TYPE_A :  result = DoA();
                            break;

    case MYMODULE_TYPE_B :  result = DoB();
                            break;

                default :   break;
  }
```

GEN often aligns `case` bodies vertically. Match the local file style.

### 11.4 Early Returns

Early returns are common and preferred over deep nesting when validating parameters:

```cpp
if(!buffer) return false;
if(!size)   return false;
```

When cleanup is required, do not early-return past cleanup. Use the existing resource lifecycle pattern.

---

## 12. Include Rules

### 12.1 Header Includes

Headers should include only what they need for declarations and member values. Use forward declarations for pointer/reference members.

Correct:

```cpp
#include "XString.h"

class XBUFFER;
class DIOSTREAM;
```

If a member is stored by value, include its full header:

```cpp
#include "XString.h"

class MYMODULE
{
  private:

    XSTRING                           name;
};
```

If only a pointer is stored, forward declare:

```cpp
class DIOSTREAM;

class MYMODULE
{
  private:

    DIOSTREAM*                        stream;
};
```

### 12.2 Source Includes

`.cpp` files include the matching header and then the headers required for implementation.

```cpp
#include "MYModule.h"

#include "XBuffer.h"
#include "XFactory.h"
#include "DIOStream.h"
```

### 12.3 Conditional Includes

Use preprocessor guards for optional features:

```cpp
#ifdef DIO_ALERTS_ACTIVE
#include "DIOAlerts.h"
#endif
```

For blocks of implementation that depend on the same flag, wrap the implementation or section consistently:

```cpp
#ifdef DIO_ALERTS_ACTIVE
...
#endif
```

---

## 13. Preprocessor Style

### 13.1 Feature Flags

GEN uses uppercase feature flags:

```cpp
#ifdef DIO_ALERTS_ACTIVE
#ifdef MICROCONTROLLER
#if defined(WINDOWS)
#if defined(LINUX) || defined(ANDROID)
```

Indent nested preprocessor blocks by two spaces when readability requires it:

```cpp
#if defined(WINDOWS)

  #if defined(BUILDER)
    #define SPRINTF             sprintf
  #else
    #define SPRINTF(str, ...)   sprintf_s(str, XSTRING_MAXTEMPOSTR-1, ## __VA_ARGS__)
  #endif

#endif
```

### 13.2 Macro Continuations

Align continuation lines:

```cpp
#define MYMODULE_CREATE(var, func)       var = MYMODULE_FACTORY.func;      \
                                          FACTORY_MODULE(var)
```

### 13.3 String Macros

Use GEN string macros for wide character literals:

```cpp
#define MYMODULE_DEFAULT_NAME            __L("module")
```

Use `__L("text")` for `XCHAR*` strings and `__C('x')` or equivalent where character macros are expected.

---

## 14. Type Usage

### 14.1 Primitive Types

Use GEN aliases:

| Use | Instead of |
|---|---|
| `XBYTE` | `uint8_t`, `unsigned char` |
| `XWORD` | `uint16_t`, `unsigned short` |
| `XDWORD` | `uint32_t`, `unsigned int` |
| `XQWORD` | `uint64_t`, `unsigned long long` |
| `XCHAR` | `wchar_t` / `char` for GEN strings |
| `XREAL` | `double` when matching GEN numeric APIs |
| `POINTER` | `void*` in GEN-facing declarations |

Standard C++ fixed-width types are not the visible style of GEN public APIs. Use them only when interacting with external libraries and convert at the boundary.

### 14.2 Strings

Use `XSTRING` and `XCHAR*` for framework-facing text.

Examples:

```cpp
XSTRING*                          GetDescription                  ();
bool                              SetName                         (XCHAR* name);
#define MYMODULE_TEXT              __L("text")
```

Do not use `std::string` in a public GEN module API unless the subsystem already does so for a specific external reason.

### 14.3 Containers

GEN commonly uses framework containers such as `XVECTOR`, `XBUFFER`, `XMAP`, etc. Prefer existing GEN containers over STL containers in public module code.

Example public style:

```cpp
XVECTOR<MYMODULE_ITEM*>             items;
XBUFFER                             xbuffer;
```

### 14.4 Booleans

Use C++ `bool` for function return values and members. `TRUE` and `FALSE` exist but many GEN APIs return `true` and `false`.

Common style:

```cpp
return true;
return false;
```

Do not introduce a new boolean typedef.

---

## 15. Memory and Ownership

### 15.1 Allocation

Use `GEN_NEW` where existing GEN classes allocate framework objects:

```cpp
object = GEN_NEW MYOBJECT();
```

Do not replace with `std::make_unique` in GEN-style modules unless the surrounding subsystem already uses smart pointers.

### 15.2 Deallocation

Use explicit deletion and set pointers to `NULL`:

```cpp
if(object)
  {
    delete object;
    object = NULL;
  }
```

When objects are created by a factory, delete them through that factory:

```cpp
if(timer)
  {
    GEN_XFACTORY.DeleteTimer(timer);
    timer = NULL;
  }
```

### 15.3 Ownership Flags

If a class may or may not own a pointer, use a boolean member with `IsOwn...` and `SetIsOwn...` accessors:

```cpp
bool                              IsOwnApplicationData             ();
void                              SetIsOwnApplicationData          (bool isownapplicationdata);

bool                              isownapplicationdata;
```

### 15.4 Avoid Exceptions

GEN style uses boolean returns and result enums, not exceptions, for normal failure handling.

Correct:

```cpp
if(!Open()) return false;
```

Incorrect for normal GEN flow:

```cpp
throw std::runtime_error("open failed");
```

---

## 16. Error and Result Conventions

### 16.1 Boolean Functions

Return `true` on success and `false` on failure.

Doxygen wording commonly says:

```cpp
* @return     bool : true if is succesful. 
```

Match the wording of the surrounding module.

### 16.2 Result Enums

For richer results, define a prefixed enum:

```cpp
enum MYMODULE_RESULT
{
  MYMODULE_RESULT_OK                  = 0 ,
  MYMODULE_RESULT_UNKNOWNCMD              ,
  MYMODULE_RESULT_NOTMEM                  ,
  MYMODULE_RESULT_ERROR                   ,
};
```

Do not return arbitrary negative integers unless the existing API uses named constants such as `NOTFOUND` or a specific error define.

### 16.3 Not Found

Use `NOTFOUND` where the existing codebase expects it:

```cpp
if(index == NOTFOUND) return false;
```

---

## 17. Comments and Documentation

### 17.1 File Banner

Every `.h` and `.cpp` starts with the GEN banner. Required fields:

```text
@file
@class
@brief
@ingroup
@copyright
@cond ... @endcond
```

Do not omit the license block.

### 17.2 Function Comments

Every non-trivial implemented class member should have a Doxygen block. Short getters and setters in existing code are often documented too; for consistency in a new module, document all methods implemented in `.cpp`.

### 17.3 Inline Comments

Use simple `//` comments for local logical sections:

```cpp
//-----------------------------------------------------------------------------------------
// UDP
```

Keep comments short and functional. Avoid narrative comments that explain obvious C++ syntax.

### 17.4 English Style

Existing comments use English but may contain legacy wording. New comments should be clear English while matching GEN vocabulary:

- Use `Ini`, not `Initialize`, when describing `Ini()`.
- Use `Del instance`, `Get instance`, etc. if matching singleton comments.
- Use module names exactly as in `@ingroup` definitions.

---

## 18. Doxygen Groups

Use existing group names from `GEN_Defines.h` where applicable:

```text
APPFLOW
CIPHER
COMMON
COMPRESS
DATABASE
DATAIO
GRAPHIC
IDENTIFICATION
INPUT
MAIN_PROCEDURE
SCRIPT
SOUND
USERINTERFACE
XUTILS
EXAMPLES
TESTS
UNIT_TESTS
PLATFORM_WINDOWS
PLATFORM_LINUX
PLATFORM_ANDROID
PLATFORM_COMMON
PLATFORM_STM32
PLATFORM_ESP32
PLATFORM_SAMD5XE5X
APPLICATION
```

The `@ingroup` in the file banner and every function block must match the subsystem.

Example:

```cpp
* @ingroup    DATAIO
```

Do not invent a new Doxygen group unless you are also updating the central group declarations.

---

## 19. Header Guards

Use only:

```cpp
#pragma once
```

Do not add traditional include guards unless the local file you are editing already uses them for a specific reason.

---

## 20. Class Member Ordering

Within the header:

1. Static singleton methods, if any.
2. Constructor/destructor.
3. Lifecycle methods: `Ini`, `End`, `Reset`.
4. State methods: `Is...`, `SetIs...`.
5. Getters/setters.
6. Domain operations: `Add...`, `Delete...`, `Read...`, `Write...`, `Send...`, `Received...`.
7. Protected/private helpers.
8. `Clean()`.
9. Static data members.
10. Instance data members.

Within the `.cpp`, implement methods in the same order as the header. Do not sort alphabetically.

---

## 21. Public API Design for a New Module

A GEN-style module should expose a compact API with familiar verbs.

Recommended skeleton:

```cpp
class MYMODULE
{
  public:
                                      MYMODULE                         ();
    virtual                          ~MYMODULE                         ();

    bool                              Ini                              ();
    bool                              End                              ();
    bool                              Reset                            ();

    bool                              IsInitialized                    ();
    void                              SetIsInitialized                 (bool isinitialized);

    XSTRING*                          GetName                          ();
    bool                              SetName                          (XCHAR* name);

    bool                              AddItem                          (MYMODULE_ITEM* item);
    MYMODULE_ITEM*                    GetItem                          (XDWORD index);
    bool                              DeleteItem                       (XDWORD index);
    bool                              DeleteAllItems                   ();

  private:

    void                              Clean                            ();

    bool                              isinitialized;
    XSTRING                           name;
    XVECTOR<MYMODULE_ITEM*>           items;
};
```

Avoid exposing STL types, exceptions, templates, lambdas, `auto`, `constexpr`, and modern constructs in public headers unless the surrounding module already uses them. GEN public APIs are intentionally stable and framework-specific.

---

## 22. Events Style

Event classes inherit from `XEVENT` and use a `_XEVENT` suffix.

Header skeleton:

```cpp
enum MYMODULE_XEVENT_TYPE
{
  MYMODULE_XEVENT_TYPE_UNKNOWN       = XEVENT_TYPE_APPLICATION ,
  MYMODULE_XEVENT_TYPE_CHANGED                                ,
};


class MYMODULE_XEVENT : public XEVENT
{
  public:
                                      MYMODULE_XEVENT                  (XSUBJECT* subject, XDWORD type = MYMODULE_XEVENT_TYPE_UNKNOWN, XDWORD family = XEVENT_TYPE_APPLICATION);
    virtual                          ~MYMODULE_XEVENT                  ();

    XDWORD                            GetValue                         ();
    void                              SetValue                         (XDWORD value);

  private:

    void                              Clean                            ();

    XDWORD                            value;
};
```

Rules:

- Event enum name is `<CLASS>_TYPE` or `<MODULE>_XEVENT_TYPE`.
- Unknown value maps to the base event family constant when appropriate.
- Constructor takes `XSUBJECT* subject`, `XDWORD type`, and `XDWORD family` where matching existing event classes.
- Store event payload as private members with getters/setters.

---

## 23. Factory Style

Factory-created classes should follow existing factory macros and methods.

Typical macro style:

```cpp
#define GEN_XFACTORY_CREATE(var, func)    var = GEN_XFACTORY.func;      \
                                          FACTORY_MODULE(var)
```

When adding a factory method:

- Use a `Create...` method name.
- Use matching `Delete...` method name.
- Return raw pointers, not smart pointers, if the existing factory does so.
- Use `GEN_NEW` internally where appropriate.
- Keep platform-specific creation behind platform factory implementations.

---

## 24. Logging and Tracing Style

GEN has framework logging/tracing utilities. Use existing macros/classes rather than adding `std::cout`, `printf`, or ad-hoc logging in module code.

Expected style examples:

```cpp
GEN_XLOG
XTRACE_PRINTCOLOR(...)
```

Rules:

- Use the subsystem's established log channel or macro.
- Do not print directly from reusable framework modules unless the surrounding module already does.
- Use `XSTRING`/`XCHAR` compatible text macros for messages.

---

## 25. Platform Conditional Code

### 25.1 Preprocessor Symbols

Use existing platform symbols:

```cpp
WINDOWS
LINUX
ANDROID
MICROCONTROLLER
BUILDER
COMPILER_MSVC
COMPILER_GCC
COMPILER_CLANG
```

### 25.2 Layout

Keep platform-specific code in platform folders when possible. If small conditional code is necessary in a common file, use `#if defined(...)` blocks:

```cpp
#if defined(WINDOWS)
  ...
#endif

#if defined(LINUX) || defined(ANDROID)
  ...
#endif
```

Do not introduce new platform abstraction names unless they are added consistently across the platform factory layer.

---

## 26. Formatting of Long Lines and Calls

GEN often aligns multi-line calls by argument columns rather than using four-space continuation indentation.

Example style:

```cpp
bool isconfig = GEN_DIOALERTS.Sender_SMTPConfig(cfg->Alerts_GetSMTPURL()->Get() , cfg->Alerts_GetSMTPPort()
                                                                                , cfg->Alerts_GetSMTPLogin()->Get()
                                                                                , cfg->Alerts_GetSMTPPassword()->Get()
                                                                                , cfg->Alerts_GetSMTPSender()->Get()
                                                                                , nrecipients[MYMODULE_TYPE_SMTP]
                                                                                , recipients[0], recipients[1], recipients[2]);
```

Rules:

- Align continuation commas under the first long argument group when the local file does this.
- Keep the expression readable over strict line length.
- Do not automatically wrap at 80 columns.
- If a call is short, keep it on one line.

---

## 27. Spacing in Declarations

GEN declaration alignment is one of the most important visible style markers.

### 27.1 Class Methods

Use columns:

```cpp
    ReturnType                         MethodName                       (Parameters);
```

Example:

```cpp
    DIOSTREAM*                         GetDIOStream                     ();
    bool                               SendCommand                      (XDWORD type, XDWORD& ID, XBUFFER& xbuffer);
```

### 27.2 Members

Use columns:

```cpp
    Type                               name;
```

Example:

```cpp
    XQWORD                             size;
    XDWORD                             crc32;
    XBYTE                              percent;
```

### 27.3 Global Variables

Use a section and simple declaration:

```cpp
MYMODULEMANAGER* MYMODULEMANAGER::instance = NULL;
```

---

## 28. Avoided Styles

Do not introduce the following styles into a GEN-style module:

```cpp
namespace gen { ... }                 // Not the visible GEN module style
class MyModule { ... };               // Wrong class casing
std::string name;                     // Wrong public framework string type
std::vector<Item> items;              // Wrong public framework container type
nullptr                              // Existing code uses NULL
auto value = ...;                     // Reduces visible type clarity in GEN style
std::unique_ptr<T> ptr;               // Existing ownership style is raw pointer + Clean/End
throw std::runtime_error(...);        // GEN uses bool/result enums
enum class Status                     // Existing style uses plain enum
// includes                            // Wrong section style
if (condition) {                      // Wrong brace style
    ...                               // Wrong indentation width
}
```

A new module should not look like modernized C++ if the neighboring GEN module does not.

---

## 29. Complete Header Template

Use this as the starting point for a new non-singleton module. Replace names, group, and includes.

```cpp
/**-------------------------------------------------------------------------------------------------------------------
* 
* @file       MYModule.h
* 
* @class      MYMODULE
* @brief      My Module class
* @ingroup    MYGROUP
* 
* @copyright  EndoraSoft. All rights reserved.
* 
* @cond
* Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated
* documentation files(the "Software"), to deal in the Software without restriction, including without limitation
* the rights to use, copy, modify, merge, publish, distribute, sublicense, and/ or sell copies of the Software,
* and to permit persons to whom the Software is furnished to do so, subject to the following conditions:
* 
* The above copyright notice and this permission notice shall be included in all copies or substantial portions of
* the Software.
* 
* THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO
* THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.IN NO EVENT SHALL THE
* AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
* TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
* SOFTWARE.
* @endcond
* 
* --------------------------------------------------------------------------------------------------------------------*/

#pragma once

/*---- INCLUDES ------------------------------------------------------------------------------------------------------*/

#include "XBase.h"
#include "XString.h"
#include "XVector.h"



/*---- DEFINES & ENUMS  ----------------------------------------------------------------------------------------------*/

#define MYMODULE_DEFAULT_TIMEOUT        5
#define MYMODULE_MAXITEMS               100


enum MYMODULE_STATUS
{
  MYMODULE_STATUS_UNKNOWN              = 0 ,
  MYMODULE_STATUS_INI                      ,
  MYMODULE_STATUS_ACTIVE                   ,
  MYMODULE_STATUS_ERROR                    ,
};



/*---- CLASS ---------------------------------------------------------------------------------------------------------*/

class XBUFFER;

class MYMODULE_ITEM
{
  public:
                                      MYMODULE_ITEM                    ();
                                     ~MYMODULE_ITEM                    ();

    XDWORD                            GetType                          ();
    void                              SetType                          (XDWORD type);

    XSTRING*                          GetName                          ();
    bool                              SetName                          (XCHAR* name);

  private:

    void                              Clean                            ();

    XDWORD                            type;
    XSTRING                           name;
};


class MYMODULE
{
  public:
                                      MYMODULE                         ();
    virtual                          ~MYMODULE                         ();

    bool                              Ini                              ();
    bool                              End                              ();
    bool                              Reset                            ();

    bool                              IsInitialized                    ();
    void                              SetIsInitialized                 (bool isinitialized);

    MYMODULE_STATUS                   GetStatus                        ();
    void                              SetStatus                        (MYMODULE_STATUS status);

    bool                              AddItem                          (MYMODULE_ITEM* item);
    MYMODULE_ITEM*                    GetItem                          (XDWORD index);
    bool                              DeleteItem                       (XDWORD index);
    bool                              DeleteAllItems                   ();

  private:

    void                              Clean                            ();

    bool                              isinitialized;
    MYMODULE_STATUS                   status;
    XVECTOR<MYMODULE_ITEM*>           items;
};



/*---- INLINE FUNCTIONS + PROTOTYPES ---------------------------------------------------------------------------------*/
```

---

## 30. Complete Source Template

```cpp
/**-------------------------------------------------------------------------------------------------------------------
* 
* @file       MYModule.cpp
* 
* @class      MYMODULE
* @brief      My Module class
* @ingroup    MYGROUP
* 
* @copyright  EndoraSoft. All rights reserved.
* 
* @cond
* Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated
* documentation files(the "Software"), to deal in the Software without restriction, including without limitation
* the rights to use, copy, modify, merge, publish, distribute, sublicense, and/ or sell copies of the Software,
* and to permit persons to whom the Software is furnished to do so, subject to the following conditions:
* 
* The above copyright notice and this permission notice shall be included in all copies or substantial portions of
* the Software.
* 
* THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO
* THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.IN NO EVENT SHALL THE
* AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
* TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
* SOFTWARE.
* @endcond
* 
* --------------------------------------------------------------------------------------------------------------------*/

/*---- PRECOMPILATION INCLUDES ---------------------------------------------------------------------------------------*/

#include "GEN_Defines.h"



/*---- INCLUDES ------------------------------------------------------------------------------------------------------*/

#include "MYModule.h"

#include "XBuffer.h"



/*---- PRECOMPILATION INCLUDES ---------------------------------------------------------------------------------------*/

#include "GEN_Control.h"




/*---- CLASS MEMBERS -------------------------------------------------------------------------------------------------*/


/**-------------------------------------------------------------------------------------------------------------------
* 
* @fn         MYMODULE_ITEM::MYMODULE_ITEM()
* @brief      Constructor of class
* @ingroup    MYGROUP
* 
* --------------------------------------------------------------------------------------------------------------------*/
MYMODULE_ITEM::MYMODULE_ITEM()
{
  Clean();
}


/**-------------------------------------------------------------------------------------------------------------------
* 
* @fn         MYMODULE_ITEM::~MYMODULE_ITEM()
* @brief      Destructor of class
* @ingroup    MYGROUP
* 
* --------------------------------------------------------------------------------------------------------------------*/
MYMODULE_ITEM::~MYMODULE_ITEM()
{
  Clean();
}


/**-------------------------------------------------------------------------------------------------------------------
* 
* @fn         XDWORD MYMODULE_ITEM::GetType()
* @brief      Get type
* @ingroup    MYGROUP
* 
* @return     XDWORD : 
* 
* --------------------------------------------------------------------------------------------------------------------*/
XDWORD MYMODULE_ITEM::GetType()
{
  return type;
}


/**-------------------------------------------------------------------------------------------------------------------
* 
* @fn         void MYMODULE_ITEM::SetType(XDWORD type)
* @brief      Set type
* @ingroup    MYGROUP
* 
* @param[in]  type : 
* 
* --------------------------------------------------------------------------------------------------------------------*/
void MYMODULE_ITEM::SetType(XDWORD type)
{
  this->type = type;
}


/**-------------------------------------------------------------------------------------------------------------------
* 
* @fn         XSTRING* MYMODULE_ITEM::GetName()
* @brief      Get name
* @ingroup    MYGROUP
* 
* @return     XSTRING* : 
* 
* --------------------------------------------------------------------------------------------------------------------*/
XSTRING* MYMODULE_ITEM::GetName()
{
  return &name;
}


/**-------------------------------------------------------------------------------------------------------------------
* 
* @fn         bool MYMODULE_ITEM::SetName(XCHAR* name)
* @brief      Set name
* @ingroup    MYGROUP
* 
* @param[in]  name : 
* 
* @return     bool : true if is succesful. 
* 
* --------------------------------------------------------------------------------------------------------------------*/
bool MYMODULE_ITEM::SetName(XCHAR* name)
{
  if(!name) return false;

  this->name.Set(name);

  return true;
}


/**-------------------------------------------------------------------------------------------------------------------
* 
* @fn         void MYMODULE_ITEM::Clean()
* @brief      Clean
* @ingroup    MYGROUP
* 
* --------------------------------------------------------------------------------------------------------------------*/
void MYMODULE_ITEM::Clean()
{
  type = 0;
  name.Empty();
}


/**-------------------------------------------------------------------------------------------------------------------
* 
* @fn         MYMODULE::MYMODULE()
* @brief      Constructor of class
* @ingroup    MYGROUP
* 
* --------------------------------------------------------------------------------------------------------------------*/
MYMODULE::MYMODULE()
{
  Clean();
}


/**-------------------------------------------------------------------------------------------------------------------
* 
* @fn         MYMODULE::~MYMODULE()
* @brief      Destructor of class
* @ingroup    MYGROUP
* 
* --------------------------------------------------------------------------------------------------------------------*/
MYMODULE::~MYMODULE()
{
  End();

  Clean();
}


/**-------------------------------------------------------------------------------------------------------------------
* 
* @fn         bool MYMODULE::Ini()
* @brief      Ini
* @ingroup    MYGROUP
* 
* @return     bool : true if is succesful. 
* 
* --------------------------------------------------------------------------------------------------------------------*/
bool MYMODULE::Ini()
{
  if(isinitialized) return true;

  isinitialized = true;
  status        = MYMODULE_STATUS_ACTIVE;

  return true;
}


/**-------------------------------------------------------------------------------------------------------------------
* 
* @fn         bool MYMODULE::End()
* @brief      End
* @ingroup    MYGROUP
* 
* @return     bool : true if is succesful. 
* 
* --------------------------------------------------------------------------------------------------------------------*/
bool MYMODULE::End()
{
  if(!isinitialized) return false;

  DeleteAllItems();

  isinitialized = false;
  status        = MYMODULE_STATUS_UNKNOWN;

  return true;
}


/**-------------------------------------------------------------------------------------------------------------------
* 
* @fn         bool MYMODULE::Reset()
* @brief      Reset
* @ingroup    MYGROUP
* 
* @return     bool : true if is succesful. 
* 
* --------------------------------------------------------------------------------------------------------------------*/
bool MYMODULE::Reset()
{
  DeleteAllItems();

  status = MYMODULE_STATUS_UNKNOWN;

  return true;
}


/**-------------------------------------------------------------------------------------------------------------------
* 
* @fn         bool MYMODULE::IsInitialized()
* @brief      Is initialized
* @ingroup    MYGROUP
* 
* @return     bool : true if is succesful. 
* 
* --------------------------------------------------------------------------------------------------------------------*/
bool MYMODULE::IsInitialized()
{
  return isinitialized;
}


/**-------------------------------------------------------------------------------------------------------------------
* 
* @fn         void MYMODULE::SetIsInitialized(bool isinitialized)
* @brief      Set is initialized
* @ingroup    MYGROUP
* 
* @param[in]  isinitialized : 
* 
* --------------------------------------------------------------------------------------------------------------------*/
void MYMODULE::SetIsInitialized(bool isinitialized)
{
  this->isinitialized = isinitialized;
}


/**-------------------------------------------------------------------------------------------------------------------
* 
* @fn         MYMODULE_STATUS MYMODULE::GetStatus()
* @brief      Get status
* @ingroup    MYGROUP
* 
* @return     MYMODULE_STATUS : 
* 
* --------------------------------------------------------------------------------------------------------------------*/
MYMODULE_STATUS MYMODULE::GetStatus()
{
  return status;
}


/**-------------------------------------------------------------------------------------------------------------------
* 
* @fn         void MYMODULE::SetStatus(MYMODULE_STATUS status)
* @brief      Set status
* @ingroup    MYGROUP
* 
* @param[in]  status : 
* 
* --------------------------------------------------------------------------------------------------------------------*/
void MYMODULE::SetStatus(MYMODULE_STATUS status)
{
  this->status = status;
}


/**-------------------------------------------------------------------------------------------------------------------
* 
* @fn         bool MYMODULE::AddItem(MYMODULE_ITEM* item)
* @brief      Add item
* @ingroup    MYGROUP
* 
* @param[in]  item : 
* 
* @return     bool : true if is succesful. 
* 
* --------------------------------------------------------------------------------------------------------------------*/
bool MYMODULE::AddItem(MYMODULE_ITEM* item)
{
  if(!item) return false;

  items.Add(item);

  return true;
}


/**-------------------------------------------------------------------------------------------------------------------
* 
* @fn         MYMODULE_ITEM* MYMODULE::GetItem(XDWORD index)
* @brief      Get item
* @ingroup    MYGROUP
* 
* @param[in]  index : 
* 
* @return     MYMODULE_ITEM* : 
* 
* --------------------------------------------------------------------------------------------------------------------*/
MYMODULE_ITEM* MYMODULE::GetItem(XDWORD index)
{
  if(index >= (XDWORD)items.GetSize()) return NULL;

  return items.Get(index);
}


/**-------------------------------------------------------------------------------------------------------------------
* 
* @fn         bool MYMODULE::DeleteItem(XDWORD index)
* @brief      Delete item
* @ingroup    MYGROUP
* 
* @param[in]  index : 
* 
* @return     bool : true if is succesful. 
* 
* --------------------------------------------------------------------------------------------------------------------*/
bool MYMODULE::DeleteItem(XDWORD index)
{
  MYMODULE_ITEM* item = GetItem(index);
  if(!item) return false;

  delete item;

  items.DeleteIndex(index);

  return true;
}


/**-------------------------------------------------------------------------------------------------------------------
* 
* @fn         bool MYMODULE::DeleteAllItems()
* @brief      Delete all items
* @ingroup    MYGROUP
* 
* @return     bool : true if is succesful. 
* 
* --------------------------------------------------------------------------------------------------------------------*/
bool MYMODULE::DeleteAllItems()
{
  if(items.IsEmpty()) return false;

  items.DeleteContents();
  items.DeleteAll();

  return true;
}


/**-------------------------------------------------------------------------------------------------------------------
* 
* @fn         void MYMODULE::Clean()
* @brief      Clean
* @ingroup    MYGROUP
* 
* --------------------------------------------------------------------------------------------------------------------*/
void MYMODULE::Clean()
{
  isinitialized = false;
  status        = MYMODULE_STATUS_UNKNOWN;
}
```

---

## 31. New Module Checklist

Before submitting a new GEN module, verify each item:

### File structure

- [ ] Header and source filenames match the local subsystem naming style.
- [ ] Header starts with the GEN banner.
- [ ] Source starts with the GEN banner.
- [ ] Header uses `#pragma once`.
- [ ] Source includes `GEN_Defines.h` first.
- [ ] Source includes `GEN_Control.h` after normal includes.
- [ ] Section separators match GEN style.

### Naming

- [ ] Class names are uppercase and prefixed.
- [ ] Enum names are uppercase and prefixed.
- [ ] Enum values are uppercase and prefixed.
- [ ] Defines are uppercase and prefixed.
- [ ] Methods use PascalCase / GEN lifecycle names.
- [ ] Member variables use lowercase compact names, without `m_` or trailing `_`.
- [ ] Getters use `Get...`.
- [ ] Setters use `Set...`.
- [ ] Boolean queries use `Is...`.

### Formatting

- [ ] Indentation is two spaces.
- [ ] No tabs were introduced.
- [ ] Braces are on their own lines for functions and block bodies.
- [ ] Declarations are aligned in columns.
- [ ] Repeated assignments are aligned where useful.
- [ ] Existing long-call alignment style is preserved.

### Lifecycle

- [ ] Constructor calls `Clean()`.
- [ ] Destructor calls `End()` when resources exist.
- [ ] Destructor calls `Clean()`.
- [ ] `Ini()` returns `bool` where initialization can fail.
- [ ] `End()` releases resources and resets state.
- [ ] `Clean()` resets all members and does not allocate.
- [ ] Deleted pointers are set to `NULL`.

### API and dependencies

- [ ] Public API uses GEN types (`XSTRING`, `XCHAR`, `XBUFFER`, `XDWORD`, etc.).
- [ ] Headers use forward declarations where possible.
- [ ] No unnecessary STL types are exposed in public headers.
- [ ] No exceptions are used for normal framework control flow.
- [ ] Factory-owned objects are created/deleted through the corresponding factory.

### Documentation

- [ ] `@file`, `@class`, `@brief`, and `@ingroup` are present.
- [ ] Every implemented method has a GEN Doxygen block.
- [ ] `@param` entries exist for every parameter.
- [ ] `@return` exists for non-void functions.
- [ ] The Doxygen group matches the subsystem.

---

## 32. Final Style Rule

When extending an existing GEN folder, the most important rule is local consistency. Open two or three nearby `.h`/`.cpp` pairs and copy their visible structure exactly:

- same banner format,
- same section labels,
- same indentation,
- same declaration alignment,
- same lifecycle method order,
- same include order,
- same macro prefix,
- same Doxygen group,
- same event/factory/singleton pattern if applicable.

A correct GEN module should look as though it was written at the same time as the surrounding files. External C++ style guides are secondary to this repository's established style.
