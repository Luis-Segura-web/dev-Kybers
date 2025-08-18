# CLAUDE.md

> Guía para Claude Code al trabajar en este repositorio. **Idioma de trabajo: español**.

> **Idioma de trabajo obligatorio: español.** 
> Responde siempre en español (UI, logs, comentarios, commits y explicaciones). 
> Si el usuario escribe en otro idioma, traduce y contesta en español.


## 1) Resumen del proyecto
- **App:** Kybers Stream (IPTV solo teléfonos Android).
- **Objetivo:** Reproductor que consume **Xtream Codes** (canales, películas, series), muestra detalles con **TMDB**, y **solo permite 1 conexión activa** a la vez (cierra la anterior antes de abrir otra).
- **Perfiles:** Múltiples perfiles/cuentas guardadas; selección al iniciar. Si no hay perfiles, mostrar formulario de alta.
- **Stack:** Kotlin, Jetpack Compose, Hilt, Retrofit/OkHttp, Media3 (ExoPlayer), Coroutines, Clean Architecture.
- **Compatibilidad:** **minSdk 24**, **targetSdk 36**. Aceptar **HTTP y HTTPS** para el servidor que provea el cliente (no limitar dominio).
- **Formatos video:** HLS **.m3u8**, **.ts** (y demás compatibles de series/películas en Media3).

## 2) Versionado y dependencias
- **Kotlin:** 2.2.x (proyecto apunta a 2.2.10; si es seguro, subir a último parche).
- **AGP:** 8.14.x.
- **Compose:** usar BOM más reciente estable y `activity-compose`.
- **DI:** Hilt + `hilt-navigation-compose`.
- **Networking:** Retrofit + OkHttp (últimos estables). JSON: Moshi o Gson (elige uno).
- **Media:** AndroidX Media3 (ExoPlayer).
> **Regla:** Siempre propone la **última versión estable** desde Maven Central/Google y valida compatibilidad. Si cambias versiones, explica el impacto.

## 3) Arquitectura y módulos
- **Capas:** `domain/`, `data/`, `app/` (presentation).
- **Domain:** casos de uso y modelos puros.
- **Data:** repositorios, mapeos DTO↔domain, servicios Retrofit, caché local (Room o DataStore según necesidad).
- **App (UI):** Compose, navegación, estados (State/Flow), inyección Hilt.
- **Reglas:** Sin lógica de negocio en UI; corrutinas en repos/casos de uso; manejo de errores centralizado.

## 4) Reglas funcionales IPTV
- **Una sola conexión activa:** al abrir un stream, cerrar el que esté activo (lifecycle-aware). Evitar fugas de recursos.
- **Perfiles:** CRUD de perfiles, selección inicial; credenciales seguras (EncryptedSharedPreferences o DataStore + cifrado).
- **TMDB:** detalles de películas/series al entrar a cada ítem (títulos, sinopsis, pósters, géneros).
- **Red:** timeouts razonables, reintentos a nivel OkHttp si aplica. Mostrar mensajes de error claros en español.
- **Compatibilidad HTTP/HTTPS:** no forzar HSTS ni bloquear HTTP; advertir al usuario si no es seguro.

## 5) Pantallas mínimas
1. **Splash** (3s) con logo/nombre.
2. **Selector de perfil** (lista de perfiles; si vacío, mostrar **Alta de perfil** con URL/usuario/contraseña/nombre del perfil).
3. **Home (Tabs abajo):** Inicio | Favoritos | TV | Películas | Series.
4. **Detalle VOD/Serie:** integrar datos de TMDB.
5. **Player:** Media3 listo para `.ts/.m3u8`, controles, reconexión, cierre limpio al salir.

## 6) Build, run y herramientas (Windows)
- **Build debug:** `.\gradlew.bat :app:assembleDebug --no-daemon --stacktrace`
- **Lint/format (si aplica):** `.\gradlew.bat ktlintCheck` / `detekt`
- **Tests:** `.\gradlew.bat test`
- **Requisitos:** `JAVA_HOME` válido, Android SDK/Platform-Tools en PATH.
> Siempre pega **comando ejecutado** y **salida**. Si falla, explica causa y solución paso a paso.

## 7) Migraciones técnicas prioritarias
1. **Compiler Options (obligatorio):** Migrar `jvmTarget` deprecado a `kotlin { compilerOptions { ... } }` y configurar `java { toolchain { ... } }`.
2. **Media3:** Confirmar reproductor con `.ts/.m3u8` y liberación de recursos al pausar/detener/fin.
3. **Dependencias:** Unificar a últimas estables compatibles (Compose BOM, Hilt, Retrofit/OkHttp, coroutines).
4. **Perfiles y seguridad:** Persistencia cifrada, selector inicial y cierre de sesión.
5. **HTTP/HTTPS:** Aceptar ambos; si HTTP, mostrar aviso de conexión no segura.

## 8) Convenciones
- **Idioma:** toda comunicación y UI en **español**.
- **Commits:** Conventional Commits (`feat:`, `fix:`, `refactor:`, etc.).
- **PR Checklist:** build verde; sin warnings críticos; linters OK; tests clave pasan; notas de versiones si rompes API.

## 9) Archivos sensibles
- No tocar ni subir llaves de firma, `keystore`, secretos TMDB, credenciales reales. Usar `.env`/`local.properties`/`gradle.properties` locales y ejemplos `*.example`.

## 10) Tareas típicas para pedir a Claude
- Revisar `app/build.gradle.kts` y **migrar a compilerOptions**; pegar diff.
- Actualizar dependencias a últimas estables y **explicar compatibilidad**.
- Añadir player Media3 con `.ts/.m3u8`, controles y manejo de ciclo de vida.
- Implementar **cierre de stream previo** al iniciar uno nuevo.
- Crear **Selector de perfiles** y **Alta de perfil** con almacenamiento seguro.
- Integrar **TMDB** (adaptadores y mapeos) en pantallas de detalle.

## 11) Criterios de aceptación (ejemplos)
- Build `assembleDebug` exitoso, sin deprecations clave (compilerOptions aplicada).
- Reproducir HLS `.m3u8` y `.ts` sin fugas (logs limpios al cerrar).
- Cambiar de canal cierra stream anterior.
- Seleccionar/crear perfil funciona y persiste datos.
- Detalles TMDB visibles en pantallas de VOD/Series.

---
