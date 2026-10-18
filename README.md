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


## Actividad de la sesión 8 y verificación actual

- [Contraste del video y las diapositivas, respuestas y ejecución](Actividad%20y%20contraste%20-%20Sesion%208.md).
- [Informe de resultados y límites de la comprobación](Estado%20y%20respuestas%20-%20Sesion%208.md).
- [Resultados de integración con la API real y PostgreSQL](Evidencias/sesion8-validacion-api-real.json).
- [Evidencias visuales](Evidencias).

El 18 de octubre de 2026 aprobaron las 15 pruebas oficiales y 17 comprobaciones de integración con la API real; el analizador no encontró problemas y las versiones web release/debug compilaron. Brave verificó registro, creación y edición, límite de 500, restauración, respaldo sin conexión, recuperación, aviso de sesión caducada y favoritos conservados. El código de aplicación ya contenía los pasos obligatorios y se mantuvo intacto en esta revisión.

### Si Docker conserva una imagen antigua

El backend fuente ya permite CORS para GET, POST, PATCH, DELETE, PUT y OPTIONS desde localhost:8186. Si el contenedor instalado devuelve solo GET/POST en un preflight, reconstruirlo desde la carpeta del backend con `docker compose up --build -d`, sin eliminar los volúmenes.

En la verificación inicial el entorno no tenía acceso al canal Docker para reconstruirlo. Se iniciaron los contenedores por Docker Desktop y se utilizó un intermediario local temporal (8002 → API real 8000) para comprobar los flujos en Brave. El informe distingue esta ejecución de la ruta directa habitual; el intermediario no forma parte de la aplicación ni es necesario con una imagen actualizada.

## Ejecución directa completada

El 18 de octubre de 2026 se reconstruyó la imagen Docker y quedó resuelta la limitación de CORS. Se verificaron **PATCH y DELETE desde Brave directamente a localhost:8000**, sin intermediario: el gasto de prueba pasó de 2.50 a 3.50 y después se eliminó con confirmación. La API confirmó la edición y la eliminación; la lista quedó vacía.

- [Edición directa comprobada](Evidencias/sesion8-10-patch-directo.png).
- [Eliminación directa comprobada](Evidencias/sesion8-12-delete-directo.png).
- [Resultado CORS de la imagen reconstruida](Evidencias/sesion8-cors-directo-final.json).

El informe mantiene la historia de la comprobación inicial y documenta el cierre del pendiente. No se borraron los volúmenes ni los gastos anteriores del curso. No se requiere el intermediario temporal para ejecutar esta práctica con la imagen actualizada.
