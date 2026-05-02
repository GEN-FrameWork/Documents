# Crear los accesos directos VSCode + MSVC en Windows

Guía paso a paso para crear los dos accesos directos que abren VSCode con el entorno MSVC ya cargado, listos para usar los perfiles `1-windows-intel32` y `2-windows-intel64` del `CMakePresets.json`.

---

## 0. Antes de empezar: pre-requisitos

Necesitas tener instalado en la máquina:

1. **Visual Studio 2022** (cualquier edición: Community, Professional, Enterprise) con la carga de trabajo *"Desarrollo para el escritorio con C++"* (que incluye `vcvarsall.bat`).
2. **Visual Studio Code**, con la opción *"Add to PATH"* marcada durante la instalación (es la opción por defecto). Si no lo recuerdas, lo verificaremos en el paso 2.

### Paso 0.1 — Verificar Visual Studio 2022

Abrir un **Símbolo del sistema** o **PowerShell** normal (sin entorno especial) y ejecutar:

```cmd
"C:\Program Files (x86)\Microsoft Visual Studio\Installer\vswhere.exe" -latest -property installationPath
```

**Resultado esperado** (similar a uno de estos según tu edición):

```
C:\Program Files\Microsoft Visual Studio\2022\Enterprise
C:\Program Files\Microsoft Visual Studio\2022\Professional
C:\Program Files\Microsoft Visual Studio\2022\Community
```

Si obtienes una ruta válida, perfecto. Si te dice *"no se encuentra el archivo"* o similar, falta `vswhere.exe`, lo cual indica que VS2022 no está bien instalado — reinstálalo con la carga de trabajo C++.

### Paso 0.2 — Verificar VSCode en PATH

En la misma ventana de cmd/PowerShell ejecutar:

```cmd
where code
```

**Resultado esperado:**

```
C:\Users\<usuario>\AppData\Local\Programs\Microsoft VS Code\bin\code.cmd
C:\Users\<usuario>\AppData\Local\Programs\Microsoft VS Code\bin\code
```

Si dice *"INFO: No se encontraron archivos para el patrón especificado: code"*, VSCode no está en PATH. Soluciones:
- Reinstalar VSCode marcando *"Add to PATH"* en el instalador.
- O añadir manualmente al PATH del usuario la carpeta `C:\Users\<usuario>\AppData\Local\Programs\Microsoft VS Code\bin`.

### Paso 0.3 — Verificar que `vcvarsall.bat` existe

```cmd
dir "C:\Program Files\Microsoft Visual Studio\2022\Enterprise\VC\Auxiliary\Build\vcvarsall.bat"
```

(sustituye `Enterprise` por tu edición real obtenida en el paso 0.1)

Si el archivo aparece listado, todo está en orden.

---

## 1. Crear el acceso directo "VSCode MSVC x64"

Este acceso directo abrirá VSCode con el entorno **MSVC x64 sobre host x64** cargado, equivalente a lo que VS2022 hace al seleccionar el preset `2 - Windows INTEL x64`.

### Paso 1.1 — Crear el acceso directo en el escritorio

1. Hacer **click derecho en una zona vacía del escritorio**.
2. En el menú contextual elegir **Nuevo → Acceso directo**.

```
┌─────────────────────────────────────────┐
│ 🔧 Ver                              ►   │
│ 📐 Ordenar por                      ►   │
│ 🔄 Actualizar                           │
│ ─────────────────────────────────────── │
│ 📋 Pegar                                │
│ 📋 Pegar acceso directo                 │
│ ─────────────────────────────────────── │
│ ➕ Nuevo                            ► ◄── pasar el cursor
│   ├ 📁 Carpeta                          │
│   ├ 🔗 Acceso directo               ◄── pulsar aquí
│   ├ 📄 Documento de texto               │
│   └ ...                                 │
└─────────────────────────────────────────┘
```

### Paso 1.2 — Pantalla "Crear acceso directo"

Aparece una ventana titulada *"Crear acceso directo"* con un campo grande etiquetado **"Escriba la ubicación del elemento:"**.

En ese campo, **copiar y pegar exactamente** la siguiente línea (es UNA SOLA línea, sin saltos):

```
cmd.exe /k "for /f "usebackq tokens=*" %i in (`"%ProgramFiles(x86)%\Microsoft Visual Studio\Installer\vswhere.exe" -latest -property installationPath`) do @call "%i\VC\Auxiliary\Build\vcvarsall.bat" x64 && code . && exit"
```

Pulsar **Siguiente >**.

### Paso 1.3 — Pantalla "Nombrar el acceso directo"

En el campo **"Escriba un nombre para este acceso directo:"** introducir:

```
VSCode MSVC x64
```

Pulsar **Finalizar**.

Aparecerá un nuevo icono en el escritorio con ese nombre. Por defecto tendrá el icono de "consola" (cmd.exe).

### Paso 1.4 — Configurar el directorio de inicio

**Click derecho** sobre el acceso directo recién creado → **Propiedades**. Se abre una ventana con varias pestañas; quedarte en la pestaña **Acceso directo**.

Verás varios campos:

```
┌─ Acceso directo ────────────────────────────────────────┐
│                                                         │
│   Tipo de destino:    Aplicación                        │
│   Ubicación de destino:  System32                       │
│                                                         │
│   Destino:           [campo grande con la línea         │
│                      cmd.exe /k "..." que pegamos]      │
│                                                         │
│   Iniciar en:        [campo VACIO o con C:\WINDOWS\...] │
│                                  ▲                      │
│                                  │                      │
│                          AQUI poner la ruta             │
│                          del proyecto                   │
└─────────────────────────────────────────────────────────┘
```

En **"Iniciar en:"** borrar lo que haya y poner la ruta a la carpeta `CMake/` de tu proyecto, por ejemplo:

```
E:\Projects\GEN_FrameWork\Examples\Console\IniBase\CMake
```

> Esta ruta determina **qué proyecto se abre** al hacer doble click. Si tienes varios proyectos GEN, te interesará crear varios accesos directos, uno por proyecto, todos con el mismo "Destino" pero distinto "Iniciar en".

Pulsar **Aplicar** y luego **Aceptar**.

### Paso 1.5 — Probar el acceso directo

Hacer **doble click** sobre el icono `VSCode MSVC x64`. Verás esta secuencia:

1. **Aparece una ventana de cmd negra** con texto similar a:
   ```
   **********************************************************************
   ** Visual Studio 2022 Developer Command Prompt v17.x.y
   ** Copyright (c) 2022 Microsoft Corporation
   **********************************************************************
   [vcvarsall.bat] Environment initialized for: 'x64'
   ```

2. **Inmediatamente después se abre VSCode** con el proyecto cargado.

3. **La ventana de cmd se cierra automáticamente** (gracias al `&& exit` final del comando).

Si VSCode se abre y la cmd desaparece, **ha funcionado**.

---

## 2. Crear el acceso directo "VSCode MSVC x86"

Repetir todo el proceso del apartado 1, pero con dos cambios:

### Paso 2.1 — La línea para el campo "Destino"

```
cmd.exe /k "for /f "usebackq tokens=*" %i in (`"%ProgramFiles(x86)%\Microsoft Visual Studio\Installer\vswhere.exe" -latest -property installationPath`) do @call "%i\VC\Auxiliary\Build\vcvarsall.bat" x86_amd64 && code . && exit"
```

> **Diferencia clave:** `x64` se ha cambiado por `x86_amd64`. Esto significa "compilador x86 ejecutándose en host x64" — exactamente lo que necesita el preset `1-windows-intel32`.

### Paso 2.2 — Nombre del acceso directo

```
VSCode MSVC x86
```

### Paso 2.3 — Iniciar en

La misma ruta que el anterior (es el mismo proyecto, solo cambia la arquitectura del compilador):

```
E:\Projects\GEN_FrameWork\Examples\Console\IniBase\CMake
```

---

## 3. Verificación: ¿está cargado el entorno MSVC?

Una vez VSCode esté abierto desde uno de los accesos directos, hacer la siguiente prueba para confirmar que el entorno está bien:

### Paso 3.1 — Abrir el terminal integrado de VSCode

Pulsar `Ctrl + ñ` (o `Ctrl + ` ` en teclados sin tilde) o ir al menú **Terminal → New Terminal**.

### Paso 3.2 — Ejecutar comandos de verificación

En el terminal integrado escribir:

```cmd
where cl
```

**Resultado esperado** (ejemplo desde el x64):
```
C:\Program Files\Microsoft Visual Studio\2022\Enterprise\VC\Tools\MSVC\14.40.33807\bin\Hostx64\x64\cl.exe
```

Si aparece una ruta a `cl.exe`, el compilador está visible. Si dice "INFO: No se encontraron archivos...", el entorno NO está cargado y los presets fallarán.

Comprobar también:

```cmd
echo %INCLUDE%
```

**Resultado esperado:** una línea muy larga con rutas separadas por `;`, conteniendo carpetas como:
```
...\VC\Tools\MSVC\14.40.33807\include;...\Windows Kits\10\Include\10.0.22621.0\ucrt;...
```

Si `INCLUDE` aparece **vacía**, el entorno no se cargó.

### Paso 3.3 — Probar con CMake Tools

1. `Ctrl+Shift+P` → escribir **CMake: Select Configure Preset** → Enter.
2. Aparece una lista con los 9 perfiles del proyecto. Seleccionar `2 - Windows INTEL x64` (si abriste el x64) o `1 - Windows INTEL x32` (si abriste el x86).
3. `Ctrl+Shift+P` → **CMake: Configure**.
4. En el panel **Output → CMake/Build** debería aparecer la salida típica de CMake terminando en algo como:
   ```
   -- Configuring done
   -- Generating done
   -- Build files have been written to: E:/Projects/.../Build/Windows/intel64
   ```
   sin ningún error de "No CMAKE_C_COMPILER could be found".

Si llegas hasta aquí: **todo funciona** y ya puedes compilar con `F7`.

---

## 4. Personalización opcional: cambiar el icono

El acceso directo aparece con el icono de cmd.exe por defecto, lo cual no ayuda a distinguirlo. Cambiarlo al icono real de VSCode mejora la experiencia visual.

### Paso 4.1 — Cambiar icono

1. Click derecho sobre el acceso directo → **Propiedades**.
2. En la pestaña **Acceso directo**, abajo a la derecha, pulsar el botón **Cambiar icono...**.
3. Aparece un cuadro avisando "El archivo cmd.exe no contiene iconos...". Pulsar **Aceptar**.
4. En el cuadro **"Buscar iconos en este archivo:"** escribir o navegar a:
   ```
   C:\Users\<TU_USUARIO>\AppData\Local\Programs\Microsoft VS Code\Code.exe
   ```
   (sustituye `<TU_USUARIO>` por tu nombre de usuario de Windows)
5. Pulsar Enter o el botón **Examinar...** y abrir el `Code.exe`.
6. Aparecerá el icono azul típico de VSCode.
7. **Aceptar → Aplicar → Aceptar**.

Para distinguir x64 de x86 visualmente, una opción es renombrarlos para que el nombre lo indique claro (ya está, son `VSCode MSVC x64` y `VSCode MSVC x86`) o añadir un sufijo en el nombre como **`📘 VSCode MSVC x64`** y **`📕 VSCode MSVC x86`** copiando emojis al campo de nombre.

---

## 5. Distribuir los accesos directos al equipo

Como las rutas pueden variar entre máquinas, lo mejor **no es copiar los archivos `.lnk`** sino documentar el procedimiento. Para facilitárselo al equipo, se puede crear un script PowerShell que genere los accesos directos automáticamente.

### Paso 5.1 — Script `Create-Shortcuts.ps1`

Guardar este archivo en el repositorio (p. ej. en `Tools/Create-Shortcuts.ps1`):

```powershell
# Create-Shortcuts.ps1
# Crea dos accesos directos en el escritorio para abrir VSCode
# con el entorno MSVC cargado (x64 y x86).
#
# Uso:
#   PS> .\Create-Shortcuts.ps1 -ProjectPath "E:\Projects\GEN_FrameWork\Examples\Console\IniBase\CMake"

param(
    [Parameter(Mandatory=$true)]
    [string]$ProjectPath
)

if (-not (Test-Path $ProjectPath)) {
    Write-Error "La ruta del proyecto no existe: $ProjectPath"
    exit 1
}

$DesktopPath = [Environment]::GetFolderPath("Desktop")
$WshShell = New-Object -ComObject WScript.Shell

function New-VSCodeShortcut {
    param(
        [string]$Name,
        [string]$Arch
    )
    
    $ShortcutPath = Join-Path $DesktopPath "$Name.lnk"
    $Shortcut = $WshShell.CreateShortcut($ShortcutPath)
    $Shortcut.TargetPath = "$env:WINDIR\System32\cmd.exe"
    $Shortcut.Arguments = "/k `"for /f `"usebackq tokens=*`" %i in (``""$env:ProgramFiles(x86)\Microsoft Visual Studio\Installer\vswhere.exe`" -latest -property installationPath``) do @call `"%i\VC\Auxiliary\Build\vcvarsall.bat`" $Arch && code . && exit`""
    $Shortcut.WorkingDirectory = $ProjectPath
    $Shortcut.IconLocation = "$env:LOCALAPPDATA\Programs\Microsoft VS Code\Code.exe,0"
    $Shortcut.Description = "Visual Studio Code con entorno MSVC $Arch"
    $Shortcut.Save()
    
    Write-Host "Creado: $ShortcutPath" -ForegroundColor Green
}

New-VSCodeShortcut -Name "VSCode MSVC x64" -Arch "x64"
New-VSCodeShortcut -Name "VSCode MSVC x86" -Arch "x86_amd64"

Write-Host ""
Write-Host "Listo. Los accesos directos apuntan a:" -ForegroundColor Cyan
Write-Host "  $ProjectPath" -ForegroundColor Cyan
```

### Paso 5.2 — Cómo lo usa el resto del equipo

Cada miembro del equipo, una sola vez por proyecto:

1. Abrir PowerShell.
2. Navegar a la carpeta `Tools` del repo.
3. Ejecutar:
   ```powershell
   .\Create-Shortcuts.ps1 -ProjectPath "E:\Projects\GEN_FrameWork\Examples\Console\IniBase\CMake"
   ```
   (sustituyendo la ruta por la que tenga su clon del repo)

Aparecen los dos iconos en el escritorio listos para usar.

> **Nota:** la primera vez que se ejecute un script PowerShell, Windows puede pedir cambiar la política de ejecución. Si da error, ejecutar primero:
> ```powershell
> Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
> ```

---

## 6. Solución de problemas

### "Windows no puede encontrar cmd.exe"

**Causa:** se ha pegado mal el campo "Destino", probablemente faltan comillas o el `cmd.exe /k` inicial.
**Solución:** click derecho → Propiedades → vuelve a copiar la línea EXACTA del paso 1.2 sobre el campo "Destino".

### Se abre la cmd, dice "Environment initialized for: 'x64'", pero VSCode no aparece

**Causa:** `code` no está en el PATH.
**Solución:** ejecutar en la cmd que se queda abierta `where code`. Si no encuentra nada, reinstalar VSCode marcando "Add to PATH" o añadirlo manualmente.

### Se abre VSCode pero al pulsar `where cl` en el terminal integrado dice "no encontrado"

**Causa A:** has abierto VSCode con su acceso directo normal (no con el `VSCode MSVC x64`). Cierra y vuelve a abrir desde el correcto.
**Causa B:** el terminal integrado de VSCode está usando PowerShell y por algún motivo no heredó las variables. Cambiar el terminal por defecto: `Ctrl+Shift+P` → **Terminal: Select Default Profile** → elegir **Command Prompt**.

### `vswhere.exe` no encontrado

**Causa:** VS2022 no está instalado o lo está pero con algún problema.
**Solución:** abrir el "Visual Studio Installer", verificar que aparece VS2022 con la carga de trabajo C++ activada, y reparar la instalación si fuera necesario.

### El acceso directo abre VSCode en la carpeta de Windows en vez del proyecto

**Causa:** el campo "Iniciar en:" está vacío o mal configurado.
**Solución:** click derecho → Propiedades → poner en "Iniciar en:" la ruta de la carpeta `CMake/` del proyecto.

### Funciona en mi máquina pero no en otra del equipo

**Causa probable:** la otra máquina tiene VS2022 en una edición distinta (Community vs Enterprise) o en una unidad distinta (D: en vez de C:).
**Solución:** el script con `vswhere` resuelve esto automáticamente. Si aún así falla, ejecutar en esa máquina:
```cmd
"C:\Program Files (x86)\Microsoft Visual Studio\Installer\vswhere.exe" -latest -property installationPath
```
para confirmar que `vswhere` la encuentra.

### Error "Set-ExecutionPolicy" al ejecutar el .ps1

**Causa:** política de ejecución de scripts restrictiva.
**Solución:** ejecutar en PowerShell como administrador:
```powershell
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
```
y aceptar con Y.

---

## 7. Resumen rápido

| Lo que quieres | Qué hacer |
|---|---|
| Compilar Windows x64 (perfil 2) | Doble click en **VSCode MSVC x64** |
| Compilar Windows x86 (perfil 1) | Doble click en **VSCode MSVC x86** |
| Compilar Linux (perfiles 3-7) | Abrir VSCode normal + `WSL: Connect to WSL` |
| Compilar Android (perfiles 8-9) | Doble click en **VSCode MSVC x64** (sirve igual) |
| Verificar entorno cargado | `where cl` en terminal integrado de VSCode |
| Replicar accesos directos en otra máquina | Ejecutar `Create-Shortcuts.ps1` con la ruta del proyecto |
