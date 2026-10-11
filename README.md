# Práctica — Sesión 8

ExploraEC. Curso MOD3: Desarrolla Aplicaciones Móviles Robustas con Flutter e IA. Desarrollo en Windows con Antigravity.

## Ejecutar

1. Instalar Flutter SDK y Docker Desktop para las sesiones 6–8. Añadir Flutter al PATH/configurar SDK en Antigravity.
2. Abrir `Sesion-8.code-workspace` en Antigravity.
3. Desde una terminal PowerShell de Antigravity, entrar en `exploraec-curso/exploraec` y ejecutar:

```powershell
flutter pub get
flutter run -d web-server --web-port 8186 --dart-define=API_BASE_URL=http://localhost:8000
```

4. Abrir la dirección mostrada en Brave. También están conservadas las configuraciones de ejecución originales del editor. En web debe utilizarse localhost para geolocalización/almacenamiento seguro.
5. Para las pruebas del proyecto, ejecutar `flutter test` desde la carpeta de la app cuando contenga pruebas.

Se conserva `pubspec.lock` para resolver las mismas dependencias. `.dart_tool` y `build` se regeneran mediante Flutter. No se incluye APK precompilado ni se certifica ejecución en Android.

## Backend local

En PowerShell de Antigravity, entrar en `exploraec-curso/proyecto-curso-spec-kit`:

```powershell
Copy-Item .env.example .env
python -c "import secrets; print(secrets.token_hex(32))"
```

Copiar el valor generado en `SECRET_KEY` del archivo `.env`, editándolo en Antigravity. No publicar `.env`. Docker Compose configura PostgreSQL automáticamente. Luego:

```powershell
docker compose up --build -d
Invoke-RestMethod http://localhost:8000/health
```

La base inicia vacía: crear una cuenta local y gastos de prueba. Los datos y secretos del equipo original no están incluidos. Ejecutar una sola sesión del backend a la vez, porque usan el puerto 8000 y nombres de contenedor iguales.

**Estado:** las 15 pruebas oficiales de la sesión 8 pasaron. La comprobación manual completa contra Docker sigue pendiente por bloqueo de acceso al motor en el entorno usado. No se afirma que esa verificación esté completada.

