# Configuración del entorno y creación de la primera aplicación Android

## 1. Metadatos

| Campo | Valor |
|---|---|
| **Duración** | 144 minutos |
| **Complejidad** | Fácil |
| **Nivel Bloom** | Aplicar |
| **Curso** | Desarrollo Android Básico con Kotlin |
| **Laboratorio** | 1 de 5 |

---

## 2. Descripción General

En este laboratorio instalarás y configurarás Android Studio Quail 3 (2026.1.3 Patch 1) con todos los componentes SDK necesarios, crearás cuatro dispositivos virtuales Android (AVDs) para las APIs 30, 35, 36 y 37, explorarás la arquitectura del sistema operativo Android identificando sus cinco capas y tipos de aplicaciones, y finalmente crearás tu primer proyecto Android llamado **HolaAndroid** usando la plantilla *Empty Views Activity*. Al concluir, ejecutarás la aplicación en el emulador AVD API 36, verificando el flujo completo desde la escritura de código hasta el despliegue. Este laboratorio establece el entorno base que utilizarás en todos los laboratorios posteriores del curso.

---

## 3. Objetivos de Aprendizaje

Al finalizar este laboratorio serás capaz de:

- [ ] Instalar Android Studio Quail 3 y configurar los SDKs de Android para las APIs 30, 35, 36 y 37 con Build-Tools 37.0.0 y Android CLI 1.0.16261425.
- [ ] Crear y verificar cuatro AVDs funcionales (API 30, 35, 36 y 37) con imágenes de sistema Google APIs x86_64.
- [ ] Identificar las cinco capas de la arquitectura de Android (Linux Kernel, HAL, ART/Bibliotecas Nativas, Framework, Aplicaciones) y los cuatro tipos de aplicaciones Android.
- [ ] Crear un proyecto Android con la plantilla *Empty Views Activity*, configurar `libs.versions.toml` y los archivos `build.gradle.kts` con las versiones exactas del curso.
- [ ] Ejecutar la aplicación "Hola Android" en el emulador AVD API 36 y verificar el flujo completo de compilación y despliegue.

---

## 4. Prerrequisitos

### Conocimientos previos

| Requisito | Descripción |
|---|---|
| Manejo básico de archivos | Crear carpetas, copiar/mover archivos, navegar el sistema de archivos |
| Instalación de software | Experiencia descargando e instalando aplicaciones en tu sistema operativo |
| Conceptos básicos de programación | Familiaridad con la idea de variables, funciones y archivos de código (no se requiere experiencia en Kotlin) |

### Acceso y recursos

| Requisito | Detalle |
|---|---|
| Procesador 64-bit | Intel Core i5/i7 8va gen+ o AMD Ryzen 5/7 con virtualización (Intel VT-x / AMD-V) habilitada en BIOS/UEFI |
| RAM | Mínimo 16 GB (recomendado 32 GB) |
| Disco | Mínimo 50 GB libres en SSD |
| Pantalla | Mínimo 1280×800 (recomendado 1920×1080) |
| GPU | Compatible con OpenGL ES 2.0 o superior |
| Internet | Conexión estable de al menos 10 Mbps |
| Sistema operativo | Windows 10/11 (64-bit), macOS 12 Monterey+, o Ubuntu 20.04/22.04 LTS |
| Cuenta Google | Opcional pero recomendada para imágenes de sistema con Google Play |

---

## 5. Entorno del Laboratorio

### Software requerido

| Componente | Versión exacta |
|---|---|
| Android Studio | Quail 3 — 2026.1.3 Patch 1 |
| Android SDK Platform | API 30, 35, 36 y 37 |
| Android SDK Build-Tools | 37.0.0 |
| Android SDK Command-line Tools | 1.0.16261425 |
| Android Emulator | Última versión estable incluida con Android Studio Quail 3 |
| Kotlin | 2.2.10 |
| Android Gradle Plugin (AGP) | 9.3.2 |
| Gradle | 9.3.2 |
| JDK | 11 (JavaVersion.VERSION_11) |
| Compose BOM | 2026.02.01 |

### Directorio de trabajo

Todos los proyectos del curso deben residir en un directorio raíz único. Crea este directorio **antes** de iniciar Android Studio:

**Windows (PowerShell):**

```powershell
New-Item -ItemType Directory -Force -Path "C:\AndroidCursoBasico"
```

**macOS / Linux (Terminal):**

```bash
mkdir -p ~/AndroidCursoBasico
```

> **Importante:** El proyecto de este laboratorio se creará dentro de `C:\AndroidCursoBasico\Lab1\HolaAndroid` (Windows) o `~/AndroidCursoBasico/Lab1/HolaAndroid` (macOS/Linux).

---

## 6. Instrucciones Paso a Paso

### Paso 1 — Descargar e instalar Android Studio Quail 3

**Objetivo:** Obtener e instalar la versión exacta de Android Studio requerida por el curso.

**Tiempo estimado:** 20 minutos

**Instrucciones:**

1. Abre tu navegador y dirígete a la página oficial de descargas:
   ```
   https://developer.android.com/studio
   ```

2. Descarga **Android Studio Quail 3 | 2026.1.3 Patch 1** para tu sistema operativo:
   - **Windows:** archivo `.exe` (aproximadamente 1.1 GB)
   - **macOS:** archivo `.dmg` (aproximadamente 1.2 GB)
   - **Linux:** archivo `.tar.gz` (aproximadamente 1.1 GB)

3. Ejecuta el instalador según tu plataforma:

   **Windows:**
   - Ejecuta el archivo `.exe` descargado.
   - Acepta los términos de licencia.
   - Mantén las opciones predeterminadas (incluye Android Virtual Device).
   - Instala en la ruta predeterminada (`C:\Program Files\Android\Android Studio`).
   - Haz clic en **Install** y espera a que finalice.

   **macOS:**
   - Abre el archivo `.dmg`.
   - Arrastra **Android Studio** a la carpeta **Applications**.
   - Abre Android Studio desde **Applications** (la primera vez, haz clic derecho → Abrir para aceptar la verificación de seguridad de macOS).

   **Linux:**
   - Extrae el archivo:
     ```bash
     tar -xzf android-studio-*.tar.gz -C /opt/
     ```
   - Ejecuta el IDE:
     ```bash
     /opt/android-studio/bin/studio.sh
     ```

4. En el asistente de primera ejecución (**Setup Wizard**):
   - Selecciona **Standard** como tipo de instalación.
   - Acepta las licencias de todos los componentes SDK.
   - Espera a que se descarguen los componentes iniciales (SDK, Emulator, Platform Tools).

5. Una vez completada la instalación, Android Studio mostrará la pantalla de bienvenida (**Welcome to Android Studio**).

**Resultado esperado:**

La pantalla de bienvenida de Android Studio Quail 3 se muestra sin errores, con las opciones para crear un nuevo proyecto, abrir un proyecto existente y configurar el IDE.

**Verificación:**

Confirma la versión exacta del IDE:

1. Desde la pantalla de bienvenida, haz clic en el menú de tres puntos (⋮) o en **Help** → **About** (en macOS: **Android Studio** → **About Android Studio**).
2. Verifica que la versión mostrada sea: **Android Studio Quail 3 | 2026.1.3 Patch 1**.

---

### Paso 2 — Configurar los SDKs de Android y herramientas de compilación

**Objetivo:** Descargar e instalar las plataformas SDK para las APIs 30, 35, 36 y 37, las Build-Tools 37.0.0 y las herramientas de línea de comandos.

**Tiempo estimado:** 20 minutos

**Instrucciones:**

1. Desde la pantalla de bienvenida de Android Studio, haz clic en **More Actions** → **SDK Manager** (o bien, si ya tienes un proyecto abierto: **File** → **Settings** → **Languages & Frameworks** → **Android SDK**; en macOS: **Android Studio** → **Settings**).

2. En la pestaña **SDK Platforms**, marca las siguientes plataformas:

   | Plataforma | API Level | Nombre interno |
   |---|---|---|
   | Android 11.0 | 30 | "R" |
   | Android 15.0 | 35 | "VanillaIceCream" |
   | Android 16.0 | 36 | — |
   | Android 17.0 | 37 | — |

   > **Nota:** Para cada API, expande la entrada haciendo clic en **Show Package Details** (esquina inferior derecha) y asegúrate de que estén marcados:
   > - **Android SDK Platform** (para la API correspondiente)
   > - **Google APIs Intel x86_64 Atom System Image** (necesaria para los AVDs)

3. Cambia a la pestaña **SDK Tools** y marca los siguientes componentes:

   | Herramienta | Versión |
   |---|---|
   | Android SDK Build-Tools | 37.0.0 |
   | Android SDK Command-line Tools (latest) | 1.0.16261425 |
   | Android Emulator | (última versión estable) |
   | Android SDK Platform-Tools | (última versión estable) |

   > **Nota:** Haz clic en **Show Package Details** para verificar las versiones exactas. Si ya tienes versiones anteriores de Build-Tools instaladas, no las desmarques; simplemente asegúrate de que la versión 37.0.0 también esté seleccionada.

4. Haz clic en **Apply** y acepta los acuerdos de licencia que aparezcan.

5. Espera a que se descarguen e instalen todos los componentes. Esto puede tomar entre 5 y 15 minutos dependiendo de tu conexión a Internet.

6. Una vez completada la instalación, anota la ruta del **Android SDK Location** que se muestra en la parte superior del SDK Manager. Las rutas típicas son:
   - **Windows:** `C:\Users\<tu_usuario>\AppData\Local\Android\Sdk`
   - **macOS:** `/Users/<tu_usuario>/Library/Android/sdk`
   - **Linux:** `/home/<tu_usuario>/Android/Sdk`

**Resultado esperado:**

El SDK Manager muestra las cuatro plataformas (API 30, 35, 36, 37) con estado **Installed**, junto con Build-Tools 37.0.0, Command-line Tools y el Emulator instalados.

**Verificación:**

Abre una terminal (PowerShell en Windows, Terminal en macOS/Linux) y ejecuta:

**Windows (PowerShell):**

```powershell
& "$env:LOCALAPPDATA\Android\Sdk\cmdline-tools\latest\bin\sdkmanager.bat" --list_installed
```

**macOS / Linux:**

```bash
~/Library/Android/sdk/cmdline-tools/latest/bin/sdkmanager --list_installed
```
*(Ajusta la ruta si tu SDK está en otra ubicación.)*

Busca en la salida las siguientes entradas:

```
  Installed packages:
    build-tools;37.0.0
    cmdline-tools;latest
    emulator
    platforms;android-30
    platforms;android-35
    platforms;android-36
    platforms;android-37
    system-images;android-30;google_apis;x86_64
    system-images;android-35;google_apis;x86_64
    system-images;android-36;google_apis;x86_64
    system-images;android-37;google_apis;x86_64
```

> **Nota:** Los nombres exactos pueden variar ligeramente. Lo importante es confirmar que las cuatro plataformas y las cuatro imágenes de sistema están presentes.

---

### Paso 3 — Crear cuatro AVDs (Dispositivos Virtuales Android)

**Objetivo:** Crear y verificar cuatro emuladores Android con las APIs 30, 35, 36 y 37 usando el perfil de hardware Pixel 6.

**Tiempo estimado:** 20 minutos

**Instrucciones:**

1. Desde la pantalla de bienvenida de Android Studio, haz clic en **More Actions** → **Virtual Device Manager** (o desde un proyecto abierto: **Tools** → **Device Manager**).

2. Haz clic en el botón **Create Virtual Device** (icono **+**).

3. **Crea el primer AVD (API 30):**

   a. En **Select Hardware**, selecciona la categoría **Phone** y elige **Pixel 6**. Haz clic en **Next**.

   b. En **System Image**, selecciona la pestaña **Other Images** (o **x86 Images**) y busca:
      - **Release Name:** correspondiente a API 30
      - **API Level:** 30
      - **ABI:** x86_64
      - **Target:** Google APIs
   
   Si la imagen no está descargada, haz clic en el enlace **Download** junto a ella y espera a que se complete. Luego selecciónala y haz clic en **Next**.

   c. En **Verify Configuration**:
      - Cambia el **AVD Name** a: `Pixel_6_API_30`
      - Deja las demás opciones con sus valores predeterminados.
      - Haz clic en **Finish**.

4. **Repite el proceso para los tres AVDs restantes:**

   | AVD Name | Perfil Hardware | API Level | Target | ABI |
   |---|---|---|---|---|
   | `Pixel_6_API_30` | Pixel 6 | 30 | Google APIs | x86_64 |
   | `Pixel_6_API_35` | Pixel 6 | 35 | Google APIs | x86_64 |
   | `Pixel_6_API_36` | Pixel 6 | 36 | Google APIs | x86_64 |
   | `Pixel_6_API_37` | Pixel 6 | 37 | Google APIs | x86_64 |

   > **Nota:** Para cada AVD, asegúrate de seleccionar la imagen de sistema correcta (API Level y Google APIs x86_64). Si alguna imagen no aparece, regresa al SDK Manager (Paso 2) y descárgala.

5. Una vez creados los cuatro AVDs, verifica que aparezcan en la lista del **Device Manager**.

6. **Inicia el AVD API 36 para verificar su funcionamiento:**
   - En el Device Manager, haz clic en el botón de **Play** (▶) junto a `Pixel_6_API_36`.
   - Espera a que el emulador arranque completamente y muestre la pantalla de inicio de Android.
   - El primer arranque puede tardar entre 2 y 5 minutos.

7. Una vez que el emulador muestre la pantalla de inicio, verifica la versión de Android:
   - En el emulador, abre **Settings** → **About emulated device** (o **About phone**).
   - Confirma que la versión de Android corresponde a API 36.

8. Cierra el emulador haciendo clic en el botón **X** de la ventana del emulador o usando el botón de apagado en la barra de herramientas del emulador.

**Resultado esperado:**

El Device Manager muestra cuatro AVDs listados: `Pixel_6_API_30`, `Pixel_6_API_35`, `Pixel_6_API_36` y `Pixel_6_API_37`. El AVD API 36 arranca correctamente y muestra la pantalla de inicio de Android.

**Verificación:**

Desde la terminal, ejecuta el siguiente comando para listar los AVDs creados:

```bash
emulator -list-avds
```

> **Nota:** Si el comando `emulator` no se reconoce, usa la ruta completa:
> - **Windows:** `%LOCALAPPDATA%\Android\Sdk\emulator\emulator.exe -list-avds`
> - **macOS/Linux:** `~/Library/Android/sdk/emulator/emulator -list-avds`

La salida debe incluir:

```
Pixel_6_API_30
Pixel_6_API_35
Pixel_6_API_36
Pixel_6_API_37
```

---

### Paso 4 — Explorar la arquitectura de Android y los tipos de aplicaciones

**Objetivo:** Relacionar los conceptos teóricos de la arquitectura de Android con los componentes SDK instalados en tu máquina, e identificar los tipos de aplicaciones Android.

**Tiempo estimado:** 15 minutos

**Instrucciones:**

1. Abre el explorador de archivos de tu sistema operativo y navega a la carpeta del Android SDK (la ruta que anotaste en el Paso 2).

2. Observa la estructura de directorios del SDK e identifica cómo se relaciona con las capas de la arquitectura de Android:

   ```
   Android SDK/
   ├── build-tools/          ← Herramientas de compilación (empaquetado de APK/AAB)
   │   └── 37.0.0/
   ├── cmdline-tools/        ← Herramientas de línea de comandos (sdkmanager, avdmanager)
   │   └── latest/
   ├── emulator/             ← Emulador Android (simula el hardware completo)
   ├── platform-tools/       ← ADB, fastboot (comunicación con dispositivos)
   ├── platforms/            ← APIs del Framework de Android (Capa 4)
   │   ├── android-30/       ← Framework API 30 (android.jar)
   │   ├── android-35/       ← Framework API 35
   │   ├── android-36/       ← Framework API 36
   │   └── android-37/       ← Framework API 37
   ├── sources/              ← Código fuente del framework (referencia)
   └── system-images/        ← Imágenes completas del SO (Capas 1-5)
       ├── android-30/
       ├── android-35/
       ├── android-36/
       └── android-37/
   ```

3. **Relaciona cada directorio con las capas de la arquitectura:**

   | Directorio SDK | Capa de arquitectura Android | Descripción |
   |---|---|---|
   | `system-images/` | Capas 1-5 completas | Imagen completa del SO que ejecuta el emulador (Kernel Linux + HAL + ART + Framework + Apps del sistema) |
   | `platforms/android-XX/` | Capa 4: Framework de Aplicaciones | Contiene `android.jar` con todas las APIs disponibles para desarrolladores (Activity Manager, Content Providers, etc.) |
   | `build-tools/` | Herramienta de desarrollo | Compila recursos, genera bytecode y empaqueta la aplicación (interactúa con ART — Capa 3) |
   | `platform-tools/` | Herramienta de desarrollo | ADB se comunica con el Kernel Linux (Capa 1) para instalar apps y depurar |
   | `emulator/` | Simulación completa | Emula el hardware físico, permitiendo ejecutar las 5 capas en tu computadora |

4. Abre una terminal y ejecuta el siguiente comando para verificar que ADB puede comunicarse con el emulador (inicia primero el AVD API 36 desde el Device Manager si no está corriendo):

   ```bash
   adb devices
   ```

   > **Ruta completa si es necesario:**
   > - **Windows:** `%LOCALAPPDATA%\Android\Sdk\platform-tools\adb.exe devices`
   > - **macOS/Linux:** `~/Library/Android/sdk/platform-tools/adb devices`

5. Con el emulador API 36 en ejecución, ejecuta el siguiente comando para ver información del sistema operativo Android que corre dentro del emulador:

   ```bash
   adb shell getprop ro.build.version.sdk
   ```

   Este comando atraviesa las capas: tu terminal → ADB (platform-tools) → Kernel Linux del emulador → propiedad del sistema. Deberías obtener `36`.

6. Ejecuta otro comando para ver la versión del runtime de Android (ART — Capa 3):

   ```bash
   adb shell getprop persist.sys.dalvik.vm.lib.2
   ```

   Deberías obtener `libart.so`, confirmando que el emulador usa ART como runtime.

7. **Reflexión sobre tipos de aplicaciones:** Basándote en la lección teórica, completa mentalmente (o en un documento aparte) la siguiente tabla comparativa:

   | Tipo de aplicación | Lenguaje | Rendimiento | Acceso a hardware | ¿Se puede crear con Android Studio? |
   |---|---|---|---|---|
   | **Nativa** | Kotlin / Java | Alto | Completo | Sí — es el enfoque de este curso |
   | **PWA** | HTML/CSS/JS | Medio | Limitado | No directamente |
   | **Híbrida** | HTML/JS + wrapper | Medio | Parcial | No directamente |
   | **KMP** | Kotlin | Alto | Completo | Sí — con plugins adicionales |

**Resultado esperado:**

Has navegado la estructura del SDK, relacionado cada directorio con las capas de la arquitectura de Android, y ejecutado comandos ADB que demuestran la comunicación entre capas. Has identificado que la carpeta `platforms/android-37/` contiene el Framework (Capa 4) con el que programarás, y que `system-images/` contiene el sistema operativo completo que ejecutan los emuladores.

**Verificación:**

Ejecuta el siguiente comando y confirma que la salida muestra el nivel de API 36:

```bash
adb shell getprop ro.build.version.sdk
```

Salida esperada:

```
36
```

---

### Paso 5 — Crear el proyecto HolaAndroid con la plantilla Empty Views Activity

**Objetivo:** Crear el primer proyecto Android del curso con la configuración exacta requerida: plantilla *Empty Views Activity*, Kotlin DSL, paquete `com.cursokotlin.android.holaandroid`, minSdk=30, compileSdk=37, targetSdk=37.

**Tiempo estimado:** 15 minutos

**Instrucciones:**

1. Cierra el emulador si está en ejecución para liberar recursos del sistema.

2. En la pantalla de bienvenida de Android Studio, haz clic en **New Project**.

3. En la pantalla **New Project**, selecciona la plantilla:
   - Categoría: **Phone and Tablet**
   - Plantilla: **Empty Views Activity**

   > ⚠️ **CRÍTICO:** NO selecciones "Empty Activity" (que genera un proyecto Compose). Debes seleccionar **"Empty Views Activity"** que genera `activity_main.xml` y usa el sistema de Views/XML.

   Haz clic en **Next**.

4. Configura el proyecto con los siguientes valores exactos:

   | Campo | Valor |
   |---|---|
   | **Name** | `HolaAndroid` |
   | **Package name** | `com.cursokotlin.android.holaandroid` |
   | **Save location** | `C:\AndroidCursoBasico\Lab1\HolaAndroid` (Windows) o `~/AndroidCursoBasico/Lab1/HolaAndroid` (macOS/Linux) |
   | **Language** | Kotlin |
   | **Minimum SDK** | API 30: Android 11.0 ("R") |
   | **Build configuration language** | Kotlin DSL (build.gradle.kts) |

   > **Nota:** Si el campo **Save location** no permite crear subdirectorios automáticamente, crea la carpeta `Lab1` manualmente antes de este paso.

5. Haz clic en **Finish** y espera a que Android Studio cree el proyecto y sincronice Gradle por primera vez. La primera sincronización puede tardar entre 3 y 10 minutos mientras Gradle descarga las dependencias.

6. Observa la barra de estado inferior de Android Studio. Cuando muestre **"Build: completed successfully"** o **"Gradle sync finished"**, el proyecto está listo.

**Resultado esperado:**

Android Studio abre el proyecto `HolaAndroid` con los archivos `MainActivity.kt` y `activity_main.xml` generados automáticamente. La sincronización de Gradle se completa sin errores.

**Verificación:**

En la vista **Project** (panel izquierdo), verifica que la estructura del proyecto incluya:

```
HolaAndroid/
├── app/
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/com/cursokotlin/android/holaandroid/
│   │   │   │   └── MainActivity.kt
│   │   │   ├── res/
│   │   │   │   ├── layout/
│   │   │   │   │   └── activity_main.xml
│   │   │   │   ├── values/
│   │   │   │   │   ├── strings.xml
│   │   │   │   │   ├── colors.xml
│   │   │   │   │   └── themes.xml
│   │   │   │   ├── drawable/
│   │   │   │   └── mipmap-*/
│   │   │   └── AndroidManifest.xml
│   │   ├── test/
│   │   └── androidTest/
│   └── build.gradle.kts           ← Archivo de configuración del módulo app
├── gradle/
│   └── libs.versions.toml         ← Catálogo de versiones
├── build.gradle.kts               ← Archivo de configuración del proyecto raíz
├── settings.gradle.kts
└── gradle.properties
```

---

### Paso 6 — Explorar y comprender la estructura del proyecto

**Objetivo:** Identificar el propósito de cada archivo y directorio clave del proyecto Android generado, comprendiendo cómo se relacionan con la arquitectura del sistema.

**Tiempo estimado:** 15 minutos

**Instrucciones:**

1. **Explora `AndroidManifest.xml`:**

   Navega a `app/src/main/AndroidManifest.xml` y ábrelo. Observa su contenido:

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <manifest xmlns:android="http://schemas.android.com/apk/res/android"
       xmlns:tools="http://schemas.android.com/tools">

       <application
           android:allowBackup="true"
           android:dataExtractionRules="@xml/data_extraction_rules"
           android:fullBackupContent="@xml/backup_rules"
           android:icon="@mipmap/ic_launcher"
           android:label="@string/app_name"
           android:roundIcon="@mipmap/ic_launcher_round"
           android:supportsRtl="true"
           android:theme="@style/Theme.HolaAndroid"
           tools:targetApi="37">
           <activity
               android:name=".MainActivity"
               android:exported="true">
               <intent-filter>
                   <action android:name="android.intent.action.MAIN" />
                   <category android:name="android.intent.category.LAUNCHER" />
               </intent-filter>
           </activity>
       </application>

   </manifest>
   ```

   **Puntos clave a identificar:**
   - `<activity android:name=".MainActivity">` — Declara la Activity principal (Capa 5: Aplicaciones).
   - `android.intent.action.MAIN` + `android.intent.category.LAUNCHER` — Indica que esta Activity es el punto de entrada de la aplicación y aparecerá en el lanzador del dispositivo.
   - `tools:targetApi="37"` — La aplicación está dirigida a API 37.

2. **Explora `MainActivity.kt`:**

   Navega a `app/src/main/java/com/cursokotlin/android/holaandroid/MainActivity.kt`:

   ```kotlin
   package com.cursokotlin.android.holaandroid

   import android.os.Bundle
   import androidx.appcompat.app.AppCompatActivity

   class MainActivity : AppCompatActivity() {
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           setContentView(R.layout.activity_main)
       }
   }
   ```

   **Puntos clave:**
   - `AppCompatActivity` — Clase base del Framework de Android (Capa 4) que gestiona el ciclo de vida de una pantalla.
   - `onCreate()` — Método del ciclo de vida que se ejecuta cuando la Activity se crea por primera vez.
   - `setContentView(R.layout.activity_main)` — Vincula el archivo de layout XML con la Activity.

3. **Explora `activity_main.xml`:**

   Navega a `app/src/main/res/layout/activity_main.xml`:

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <androidx.constraintlayout.widget.ConstraintLayout
       xmlns:android="http://schemas.android.com/apk/res/android"
       xmlns:app="http://schemas.android.com/apk/res-auto"
       xmlns:tools="http://schemas.android.com/tools"
       android:id="@+id/main"
       android:layout_width="match_parent"
       android:layout_height="match_parent"
       tools:context=".MainActivity">

       <TextView
           android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:text="Hello World!"
           app:layout_constraintBottom_toBottomOf="parent"
           app:layout_constraintEnd_toEndOf="parent"
           app:layout_constraintStart_toStartOf="parent"
           app:layout_constraintTop_toTopOf="parent" />

   </androidx.constraintlayout.widget.ConstraintLayout>
   ```

   **Puntos clave:**
   - `ConstraintLayout` — Layout flexible que posiciona elementos mediante restricciones.
   - `TextView` — Widget que muestra texto en pantalla (actualmente "Hello World!").
   - Las restricciones (`constraintTop`, `constraintBottom`, etc.) centran el texto en la pantalla.

4. **Explora la carpeta `res/`:**

   | Subdirectorio | Contenido |
   |---|---|
   | `layout/` | Archivos XML que definen la interfaz de usuario |
   | `values/` | Strings, colores, temas y dimensiones |
   | `drawable/` | Imágenes vectoriales y recursos gráficos |
   | `mipmap-*/` | Iconos de la aplicación en diferentes densidades de pantalla |

5. **Explora `res/values/strings.xml`:**

   ```xml
   <resources>
       <string name="app_name">HolaAndroid</string>
   </resources>
   ```

   Este archivo centraliza las cadenas de texto de la aplicación, facilitando la internacionalización.

**Resultado esperado:**

Has identificado el propósito de cada archivo principal del proyecto: `AndroidManifest.xml` (declaración de componentes), `MainActivity.kt` (lógica de la pantalla principal), `activity_main.xml` (interfaz de usuario), y la carpeta `res/` (recursos de la aplicación).

**Verificación:**

Responde mentalmente las siguientes preguntas:
- ¿Qué archivo declara que `MainActivity` es el punto de entrada de la aplicación? → `AndroidManifest.xml`
- ¿Qué método de `MainActivity` vincula el layout XML? → `setContentView()`
- ¿En qué carpeta se encuentran los archivos de interfaz de usuario? → `res/layout/`

---

### Paso 7 — Configurar el catálogo de versiones `libs.versions.toml` y los archivos Gradle

**Objetivo:** Configurar los archivos de construcción del proyecto con las versiones exactas requeridas por el curso, usando el catálogo de versiones centralizado.

**Tiempo estimado:** 20 minutos

**Instrucciones:**

1. **Abre `gradle/libs.versions.toml`:**

   En el panel **Project** de Android Studio, navega a `gradle/libs.versions.toml`. Este archivo es el **catálogo centralizado de versiones** donde se definen todas las dependencias del proyecto.

2. **Reemplaza todo el contenido** del archivo con la siguiente configuración exacta:

   ```toml
   [versions]
   agp = "9.3.2"
   kotlin = "2.2.10"
   coreKtx = "1.19.0"
   lifecycleRuntimeKtx = "2.6.1"
   activityCompose = "1.13.0"
   composeBom = "2026.02.01"
   junit = "4.13.2"
   junitExt = "1.3.0"
   espressoCore = "3.7.0"
   appcompat = "1.7.0"
   material = "1.12.0"
   constraintlayout = "2.2.1"

   [libraries]
   androidx-core-ktx = { group = "androidx.core", name = "core-ktx", version.ref = "coreKtx" }
   androidx-lifecycle-runtime-ktx = { group = "androidx.lifecycle", name = "lifecycle-runtime-ktx", version.ref = "lifecycleRuntimeKtx" }
   androidx-activity-compose = { group = "androidx.activity", name = "activity-compose", version.ref = "activityCompose" }
   androidx-compose-bom = { group = "androidx.compose", name = "compose-bom", version.ref = "composeBom" }
   androidx-appcompat = { group = "androidx.appcompat", name = "appcompat", version.ref = "appcompat" }
   material = { group = "com.google.android.material", name = "material", version.ref = "material" }
   androidx-constraintlayout = { group = "androidx.constraintlayout", name = "constraintlayout", version.ref = "constraintlayout" }
   junit = { group = "junit", name = "junit", version.ref = "junit" }
   androidx-junit = { group = "androidx.test.ext", name = "junit", version.ref = "junitExt" }
   androidx-espresso-core = { group = "androidx.test.espresso", name = "espresso-core", version.ref = "espressoCore" }

   [plugins]
   android-application = { id = "com.android.application", version.ref = "agp" }
   kotlin-android = { id = "org.jetbrains.kotlin.android", version.ref = "kotlin" }
   ```

   > ⚠️ **IMPORTANTE:** No uses versiones dinámicas como `latest`, `+` o `1.x`. Todas las versiones deben estar explícitamente definidas tal como se muestra arriba.

3. **Abre el archivo `build.gradle.kts` del proyecto raíz** (el que está en la raíz del proyecto, NO dentro de `app/`):

   Reemplaza su contenido con:

   ```kotlin
   // Top-level build file where you can add configuration options common to all sub-projects/modules.
   plugins {
       alias(libs.plugins.android.application) apply false
       alias(libs.plugins.kotlin.android) apply false
   }
   ```

4. **Abre el archivo `build.gradle.kts` del módulo `app`** (ubicado en `app/build.gradle.kts`):

   Reemplaza su contenido con la siguiente configuración exacta:

   ```kotlin
   plugins {
       alias(libs.plugins.android.application)
       alias(libs.plugins.kotlin.android)
   }

   android {
       namespace = "com.cursokotlin.android.holaandroid"
       compileSdk = 37

       defaultConfig {
           applicationId = "com.cursokotlin.android.holaandroid"
           minSdk = 30
           targetSdk = 37
           versionCode = 1
           versionName = "1.0"

           testInstrumentationRunner = "androidx.test.runner.AndroidJUnitRunner"
       }

       buildTypes {
           release {
               isMinifyEnabled = false
               proguardFiles(
                   getDefaultProguardFile("proguard-android-optimize.txt"),
                   "proguard-rules.pro"
               )
           }
       }

       compileOptions {
           sourceCompatibility = JavaVersion.VERSION_11
           targetCompatibility = JavaVersion.VERSION_11
       }

       kotlinOptions {
           jvmTarget = "11"
       }

       buildFeatures {
           viewBinding = true
       }
   }

   dependencies {
       implementation(libs.androidx.core.ktx)
       implementation(libs.androidx.appcompat)
       implementation(libs.material)
       implementation(libs.androidx.constraintlayout)
       implementation(libs.androidx.lifecycle.runtime.ktx)

       testImplementation(libs.junit)
       androidTestImplementation(libs.androidx.junit)
       androidTestImplementation(libs.androidx.espresso.core)
   }
   ```

   **Puntos clave de esta configuración:**

   | Parámetro | Valor | Propósito |
   |---|---|---|
   | `compileSdk` | 37 | Compila contra la API 37 (Android 17) |
   | `minSdk` | 30 | La app se ejecuta en dispositivos con API 30 o superior |
   | `targetSdk` | 37 | La app está optimizada para API 37 |
   | `jvmTarget` | "11" | Genera bytecode compatible con JDK 11 |
   | `sourceCompatibility` | VERSION_11 | Código Java compatible con JDK 11 |
   | `viewBinding` | true | Habilita View Binding para acceso seguro a vistas |

5. **Sincroniza Gradle:**

   Haz clic en el banner amarillo **"Sync Now"** que aparece en la parte superior del editor, o bien usa **File** → **Sync Project with Gradle Files**.

6. Espera a que la sincronización se complete exitosamente. La barra de estado inferior debe mostrar **"BUILD SUCCESSFUL"** o **"Gradle sync finished"**.

**Resultado esperado:**

La sincronización de Gradle se completa sin errores. Los archivos `libs.versions.toml` y ambos `build.gradle.kts` están configurados con las versiones exactas del curso.

**Verificación:**

1. Abre la ventana **Build** en la parte inferior de Android Studio (pestaña **Build**).
2. Confirma que no hay errores (texto en rojo) en la salida de la sincronización.
3. Verifica en `libs.versions.toml` que `kotlin = "2.2.10"` y `agp = "9.3.2"` están presentes.
4. Verifica en `app/build.gradle.kts` que `compileSdk = 37`, `minSdk = 30` y `targetSdk = 37`.

---

### Paso 8 — Modificar la interfaz y personalizar el mensaje

**Objetivo:** Modificar el layout `activity_main.xml` para mostrar el mensaje personalizado "Hola Android - [Tu Nombre]" y comprender cómo se conecta el layout con la Activity.

**Tiempo estimado:** 10 minutos

**Instrucciones:**

1. Abre el archivo `app/src/main/res/layout/activity_main.xml`.

2. Cambia la vista al modo **Code** (pestaña en la esquina superior derecha del editor: **Code** | Split | Design).

3. Modifica el `TextView` existente para agregar un `id` y cambiar el texto. Reemplaza **todo el contenido** del archivo con:

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <androidx.constraintlayout.widget.ConstraintLayout
       xmlns:android="http://schemas.android.com/apk/res/android"
       xmlns:app="http://schemas.android.com/apk/res-auto"
       xmlns:tools="http://schemas.android.com/tools"
       android:id="@+id/main"
       android:layout_width="match_parent"
       android:layout_height="match_parent"
       tools:context=".MainActivity">

       <TextView
           android:id="@+id/tvSaludo"
           android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:text="@string/saludo_mensaje"
           android:textSize="24sp"
           android:textStyle="bold"
           android:textColor="@color/black"
           app:layout_constraintBottom_toBottomOf="parent"
           app:layout_constraintEnd_toEndOf="parent"
           app:layout_constraintStart_toStartOf="parent"
           app:layout_constraintTop_toTopOf="parent" />

   </androidx.constraintlayout.widget.ConstraintLayout>
   ```

   **Cambios realizados:**
   - Se agregó `android:id="@+id/tvSaludo"` para identificar el TextView.
   - Se cambió `android:text` a una referencia de recurso string: `@string/saludo_mensaje`.
   - Se agregó `android:textSize="24sp"` para un tamaño de texto más legible.
   - Se agregó `android:textStyle="bold"` para texto en negrita.
   - Se agregó `android:textColor="@color/black"` para texto en color negro.

4. Abre el archivo `app/src/main/res/values/strings.xml` y reemplaza su contenido con:

   ```xml
   <resources>
       <string name="app_name">HolaAndroid</string>
       <string name="saludo_mensaje">Hola Android - Tu Nombre</string>
   </resources>
   ```

   > **Acción requerida:** Reemplaza `Tu Nombre` con tu nombre real. Por ejemplo: `Hola Android - María García`.

5. **Opcional — Actualiza `MainActivity.kt` para usar View Binding:**

   Abre `app/src/main/java/com/cursokotlin/android/holaandroid/MainActivity.kt` y reemplaza su contenido con:

   ```kotlin
   package com.cursokotlin.android.holaandroid

   import android.os.Bundle
   import androidx.appcompat.app.AppCompatActivity
   import com.cursokotlin.android.holaandroid.databinding.ActivityMainBinding

   class MainActivity : AppCompatActivity() {

       private lateinit var binding: ActivityMainBinding

       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           binding = ActivityMainBinding.inflate(layoutInflater)
           setContentView(binding.root)

           // Acceso seguro al TextView mediante View Binding
           binding.tvSaludo.text = getString(R.string.saludo_mensaje)
       }
   }
   ```

   **Explicación del código:**
   - `ActivityMainBinding` — Clase generada automáticamente por View Binding a partir de `activity_main.xml`.
   - `binding.tvSaludo` — Acceso directo y seguro al `TextView` con id `tvSaludo`, sin necesidad de `findViewById()`.
   - `getString(R.string.saludo_mensaje)` — Obtiene el texto del recurso string definido en `strings.xml`.

6. Cambia a la vista **Split** o **Design** en `activity_main.xml` para previsualizar el layout. Deberías ver el texto "Hola Android - Tu Nombre" centrado en la pantalla.

**Resultado esperado:**

El archivo `activity_main.xml` muestra un `TextView` centrado con el texto personalizado. El archivo `MainActivity.kt` usa View Binding para acceder al `TextView` de forma segura. La previsualización del layout muestra el mensaje correctamente.

**Verificación:**

1. En la vista **Design** de `activity_main.xml`, confirma que el texto aparece centrado y con estilo bold.
2. Verifica que no hay errores de compilación (sin subrayados rojos en el código).
3. En `strings.xml`, confirma que la cadena `saludo_mensaje` contiene tu nombre.

---

### Paso 9 — Compilar y ejecutar la aplicación en el emulador AVD API 36

**Objetivo:** Ejecutar la aplicación HolaAndroid en el emulador Pixel_6_API_36, verificando el flujo completo de compilación, empaquetado y despliegue.

**Tiempo estimado:** 9 minutos

**Instrucciones:**

1. En la barra de herramientas superior de Android Studio, localiza el **selector de dispositivo** (dropdown que muestra el dispositivo de destino).

2. Haz clic en el selector y elige **Pixel_6_API_36**. Si no aparece en la lista, haz clic en **Device Manager** y verifica que el AVD existe (Paso 3).

3. Haz clic en el botón **Run** (▶ verde) en la barra de herramientas, o usa el atajo de teclado:
   - **Windows/Linux:** `Shift + F10`
   - **macOS:** `Control + R`

4. Observa la ventana **Build** en la parte inferior de Android Studio. El proceso de compilación incluye:
   - **Compilación de Kotlin:** Convierte `MainActivity.kt` a bytecode.
   - **Procesamiento de recursos:** Compila `activity_main.xml`, `strings.xml`, etc.
   - **Empaquetado:** Genera el APK (Android Package).
   - **Instalación:** Envía el APK al emulador vía ADB.
   - **Lanzamiento:** Inicia la Activity principal.

5. El emulador se iniciará automáticamente (si no estaba corriendo) y, tras el arranque, la aplicación se instalará y ejecutará. La primera compilación puede tardar entre 1 y 3 minutos.

6. Verifica que en la pantalla del emulador aparece el texto **"Hola Android - [Tu Nombre]"** centrado, en negrita y con tamaño 24sp.

7. **Explora la aplicación en el emulador:**
   - Presiona el botón **Home** (círculo en la barra de navegación del emulador) para ir a la pantalla de inicio.
   - Busca el icono de **HolaAndroid** en el cajón de aplicaciones.
   - Toca el icono para reabrir la aplicación.

**Resultado esperado:**

La aplicación HolaAndroid se ejecuta en el emulador Pixel_6_API_36, mostrando el mensaje "Hola Android - [Tu Nombre]" centrado en la pantalla con texto en negrita de 24sp.

**Verificación:**

1. Confirma visualmente que el mensaje se muestra correctamente en el emulador.

2. Abre la ventana **Logcat** en la parte inferior de Android Studio (pestaña **Logcat**).

3. En el filtro de Logcat, selecciona el proceso `com.cursokotlin.android.holaandroid` y busca mensajes de inicio de la Activity. Deberías ver entradas como:

   ```
   D/ActivityThread: callActivityOnCreate
   ```

4. Desde la terminal, verifica que la aplicación está instalada en el emulador:

   ```bash
   adb shell pm list packages | grep holaandroid
   ```

   Salida esperada:

   ```
   package:com.cursokotlin.android.holaandroid
   ```

5. Verifica la información del APK instalado:

   ```bash
   adb shell dumpsys package com.cursokotlin.android.holaandroid | grep -E "versionCode|versionName|targetSdk|minSdk"
   ```

   Salida esperada (los valores deben coincidir con tu configuración):

   ```
       versionCode=1 minSdk=30 targetSdk=37
       versionName=1.0
   ```

---

## 7. Validación y Pruebas

Realiza las siguientes verificaciones finales para confirmar que tu entorno está completamente configurado:

### Verificación 1: Entorno de desarrollo

| Elemento | Comando o acción | Resultado esperado |
|---|---|---|
| Versión de Android Studio | **Help** → **About** | Quail 3 — 2026.1.3 Patch 1 |
| SDKs instalados | SDK Manager → SDK Platforms | API 30, 35, 36, 37 con estado **Installed** |
| Build-Tools | SDK Manager → SDK Tools | 37.0.0 instalado |
| Command-line Tools | SDK Manager → SDK Tools | 1.0.16261425 instalado |

### Verificación 2: AVDs funcionales

Ejecuta desde la terminal:

```bash
emulator -list-avds
```

Resultado esperado (4 AVDs):

```
Pixel_6_API_30
Pixel_6_API_35
Pixel_6_API_36
Pixel_6_API_37
```

### Verificación 3: Proyecto HolaAndroid

| Archivo | Verificación |
|---|---|
| `libs.versions.toml` | `kotlin = "2.2.10"`, `agp = "9.3.2"`, `composeBom = "2026.02.01"` |
| `app/build.gradle.kts` | `compileSdk = 37`, `minSdk = 30`, `targetSdk = 37`, `jvmTarget = "11"` |
| `AndroidManifest.xml` | `<activity android:name=".MainActivity">` con intent-filter MAIN/LAUNCHER |
| `strings.xml` | Contiene `saludo_mensaje` con tu nombre |

### Verificación 4: Ejecución exitosa

1. Inicia el AVD `Pixel_6_API_36`.
2. Ejecuta la aplicación HolaAndroid (▶).
3. Confirma que el mensaje personalizado aparece centrado en pantalla.
4. Ejecuta:

   ```bash
   adb shell pm list packages | grep holaandroid
   ```

   Confirma que devuelve `package:com.cursokotlin.android.holaandroid`.

### Verificación 5 (Opcional): Prueba en AVD API 30

Para confirmar la compatibilidad con `minSdk = 30`:

1. Cambia el dispositivo de destino a `Pixel_6_API_30` en el selector de dispositivo.
2. Ejecuta la aplicación (▶).
3. Confirma que la aplicación se instala y muestra el mensaje correctamente en API 30.

---

## 8. Solución de Problemas

### Problema 1: El emulador no arranca o se congela en "Android is starting..."

**Síntomas:**
- Al iniciar un AVD, la ventana del emulador aparece pero se queda en la pantalla de carga con el logo de Android o el mensaje "Android is starting..." durante más de 5 minutos.
- En algunos casos, el emulador se cierra inesperadamente sin mostrar error visible.

**Causa:**
La virtualización por hardware (Intel VT-x / AMD-V / Hyper-V) no está habilitada en el BIOS/UEFI, o hay un conflicto con otro hipervisor (por ejemplo, Hyper-V activo en Windows interfiere con HAXM). También puede ocurrir si la GPU del equipo no soporta correctamente la aceleración gráfica del emulador.

**Solución:**

1. **Verifica la virtualización en BIOS/UEFI:**
   - Reinicia tu computadora y entra al BIOS/UEFI (generalmente presionando `F2`, `F12`, `Del` o `Esc` durante el arranque).
   - Busca la opción **Intel VT-x**, **Intel Virtualization Technology**, **AMD-V** o **SVM Mode** y asegúrate de que esté **Enabled**.
   - Guarda y reinicia.

2. **En Windows, verifica el estado de Hyper-V:**
   ```powershell
   # Verificar si Hyper-V está habilitado
   Get-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V
   ```
   Si Hyper-V está habilitado, Android Studio debería usar WHPX en lugar de HAXM. Verifica en **SDK Manager** → **SDK Tools** que **Android Emulator Hypervisor Driver (AEHD)** o **Windows Hypervisor Platform** esté instalado.

3. **Prueba con renderizado por software:**
   - En el **Device Manager**, haz clic en el icono de lápiz (✏️) junto al AVD problemático.
   - En **Emulated Performance** → **Graphics**, cambia de **Automatic** a **Software - GLES 2.0**.
   - Haz clic en **Finish** e intenta iniciar el AVD nuevamente.

4. **Aumenta la RAM asignada al AVD:**
   - Edita el AVD y en **Show Advanced Settings**, incrementa la **RAM** a 2048 MB o más.

---

### Problema 2: Error de sincronización Gradle "Could not resolve" o "Failed to find target"

**Síntomas:**
- Al abrir el proyecto o hacer **Sync Now**, la ventana Build muestra errores como:
  ```
  Could not resolve com.android.tools.build:gradle:9.3.2
  ```
  o
  ```
  Failed to find target with hash string 'android-37'
  ```
- El proyecto muestra iconos de error (❌) en los archivos Gradle.

**Causa:**
Las dependencias no pudieron descargarse porque: (a) la conexión a Internet está inactiva o bloqueada por un proxy/firewall corporativo, (b) los repositorios Maven (Google, Maven Central) no están configurados correctamente en `settings.gradle.kts`, o (c) la plataforma SDK requerida (android-37) no está instalada.

**Solución:**

1. **Verifica tu conexión a Internet:**
   ```bash
   ping dl.google.com
   ```
   Si no hay respuesta, verifica tu conexión de red.

2. **Verifica que `settings.gradle.kts` incluya los repositorios correctos:**

   Abre `settings.gradle.kts` en la raíz del proyecto y confirma que contiene:

   ```kotlin
   pluginManagement {
       repositories {
           google {
               content {
                   includeGroupByRegex("com\\.android.*")
                   includeGroupByRegex("com\\.google.*")
                   includeGroupByRegex("androidx.*")
               }
           }
           mavenCentral()
           gradlePluginPortal()
       }
   }
   dependencyResolutionManagement {
       repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
       repositories {
           google()
           mavenCentral()
       }
   }

   rootProject.name = "HolaAndroid"
   include(":app")
   ```

3. **Si el error es "Failed to find target android-37":**
   - Abre **SDK Manager** (**File** → **Settings** → **Languages & Frameworks** → **Android SDK**).
   - En la pestaña **SDK Platforms**, verifica que **Android 17.0 (API 37)** esté instalado.
   - Si no lo está, márcalo, haz clic en **Apply** y descárgalo.

4. **Invalida cachés y reinicia:**
   - Ve a **File** → **Invalidate Caches...** → selecciona todas las opciones → **Invalidate and Restart**.
   - Después del reinicio, haz **File** → **Sync Project with Gradle Files**.

5. **Si estás detrás de un proxy corporativo:**
   - Ve a **File** → **Settings** → **Appearance & Behavior** → **System Settings** → **HTTP Proxy**.
   - Configura los datos de tu proxy (host, puerto, credenciales si aplica).

---

## 9. Limpieza

Este laboratorio establece el entorno base para todo el curso, por lo que **no debes eliminar** ninguno de los componentes instalados. Sin embargo, realiza las siguientes acciones de mantenimiento:

1. **Cierra los emuladores en ejecución** para liberar recursos del sistema:
   - Cierra la ventana del emulador o usa el botón de apagado en la barra del emulador.

2. **Verifica el espacio en disco utilizado:**

   | Componente | Ubicación típica (Windows) | Tamaño aproximado |
   |---|---|---|
   | Android Studio | `C:\Program Files\Android\Android Studio` | ~2 GB |
   | Android SDK | `C:\Users\<usuario>\AppData\Local\Android\Sdk` | ~15-25 GB |
   | AVDs | `C:\Users\<usuario>\.android\avd\` | ~8-12 GB (4 AVDs) |
   | Proyecto HolaAndroid | `C:\AndroidCursoBasico\Lab1\HolaAndroid` | ~200-500 MB |
   | Caché de Gradle | `C:\Users\<usuario>\.gradle\` | ~2-5 GB |

3. **Si necesitas liberar espacio temporalmente** (no recomendado hasta finalizar el curso):
   - Puedes eliminar AVDs que no estés usando activamente desde el **Device Manager** (icono de menú → **Delete**).
   - Puedes limpiar la caché de Gradle con: **File** → **Invalidate Caches...** (esto forzará re-descarga en la próxima compilación).

4. **Mantén el proyecto HolaAndroid** en `C:\AndroidCursoBasico\Lab1\HolaAndroid` (o `~/AndroidCursoBasico/Lab1/HolaAndroid`). Lo necesitarás como referencia en laboratorios posteriores.

---

## 10. Resumen

### Lo que lograste en este laboratorio

En este laboratorio completaste la configuración completa del entorno de desarrollo Android y creaste tu primera aplicación funcional:

| Logro | Detalle |
|---|---|
| **Instalación del IDE** | Android Studio Quail 3 (2026.1.3 Patch 1) instalado y verificado |
| **SDKs configurados** | Plataformas API 30, 35, 36 y 37 con Build-Tools 37.0.0 y CLI 1.0.16261425 |
| **4 AVDs creados** | Pixel_6_API_30, Pixel_6_API_35, Pixel_6_API_36, Pixel_6_API_37 |
| **Arquitectura explorada** | Identificaste las 5 capas de Android y los 4 tipos de aplicaciones en los componentes SDK instalados |
| **Proyecto HolaAndroid** | Creado con plantilla Empty Views Activity, Kotlin DSL, paquete `com.cursokotlin.android.holaandroid` |
| **Gradle configurado** | `libs.versions.toml` con versiones fijas, `build.gradle.kts` con compileSdk=37, minSdk=30, targetSdk=37, JDK 11 |
| **Ejecución exitosa** | Aplicación "Hola Android - [Tu Nombre]" desplegada en emulador AVD API 36 |

### Relación con la arquitectura de Android

Durante este laboratorio interactuaste con múltiples capas de la arquitectura de Android:

- **Capa 5 (Aplicaciones):** Creaste `MainActivity` y el layout `activity_main.xml`.
- **Capa 4 (Framework):** Usaste `AppCompatActivity`, `setContentView()`, `getString()` y View Binding.
- **Capa 3 (ART):** Gradle compiló tu código Kotlin a bytecode que ART ejecuta en el emulador.
- **Capa 2 (HAL) y Capa 1 (Kernel):** El emulador simula el hardware y el kernel Linux sobre el que corre Android.
- **ADB (Platform Tools):** Verificaste la comunicación entre tu máquina de desarrollo y el dispositivo virtual.

### Próximo laboratorio

En el **Laboratorio 2** trabajarás con los fundamentos de Kotlin, creando el proyecto **KotlinTasks** donde practicarás variables, funciones, clases, colecciones y estructuras de control que necesitarás para construir aplicaciones Android más complejas.

### Recursos adicionales

| Recurso | URL |
|---|---|
| Documentación oficial de Android Studio | https://developer.android.com/studio/intro |
| Arquitectura de la plataforma Android | https://developer.android.com/guide/platform |
| Guía de creación de AVDs | https://developer.android.com/studio/run/managing-avds |
| Catálogo de versiones Gradle (libs.versions.toml) | https://developer.android.com/build/migrate-to-catalogs |
| View Binding en Android | https://developer.android.com/topic/libraries/view-binding |
| Android Runtime (ART) | https://source.android.com/docs/core/runtime |
| Kotlin como lenguaje oficial de Android | https://developer.android.com/kotlin |
