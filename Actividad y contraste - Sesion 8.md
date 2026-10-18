# Actividad de la sesión 8 — Autenticación segura y CRUD completo

Revisión del 18 de octubre de 2026. Desarrollo y comprobación en Windows; aplicación en `exploraec-curso/exploraec`, workspace `Sesion-8.code-workspace` para Antigravity.

## Contraste de video, diapositivas e implementación

Se revisaron la teoría, la demostración práctica y el cierre del video «VIDEO SESIÓN 8: 08 DE OCTUBRE» de Moodle, junto con las 39 diapositivas y el instructivo de la práctica. Los tres materiales coinciden en construir autenticación con JWT, persistencia del token, CRUD y manejo de errores sobre el backend propio. No solicitan grabar un video del estudiante: «compartiendo pantalla» describe la clase en vivo.

| Tema | Referencia en video | Diapositivas | Aplicación existente |
| --- | --- | --- | --- |
| Backend como autoridad y aislamiento de cuentas | 08:03–20:58 | 3–8 | El servidor valida el JWT y la propiedad del gasto; Hive solo conserva una copia por usuario. |
| Login como formulario y registro como JSON | 28:53–32:42; 1:20:35–1:33:09 | 14–17 | AuthController registra y encadena el login; usa username/password y content type de formulario. |
| Interceptor de token y tratamiento de 401 | 37:46–42:35; 1:18:37–1:19:40 | 20–22 | ApiClient añade Bearer y avisa de sesión caducada, exceptuando el endpoint de login. |
| Token guardado y restauración al arrancar | 34:46–37:17; 1:33:18–1:38:55 | 18–19, 27 | SecureTokenStorage y restaurarSesion consultan el usuario del token. Un error de red no lo elimina. |
| Crear, editar y eliminar gastos | 1:39:12–1:49:52 | 29–32 | Categorías del backend, POST, PATCH parcial, DELETE y recarga posterior de lista/caché. |
| Error de límite, desconexión y sesión inválida | 44:36–45:50; 1:44:43–1:52:52 | 23–25 | Mensajes del servidor; formulario abierto si hay rechazo; respaldo Hive con aviso y Reintentar. |
| Limpieza al cerrar sesión | 53:19–54:27 | 33 | Elimina token, lista y caja personal; conserva favoritos. |
| Guardar el trabajo | 1:53:04–1:53:32 | Cierre de práctica | Commit y push al repositorio de la práctica. |

### Precisiones técnicas

- En la respuesta oral de aproximadamente 1:00:38–1:01:01 se afirma que cerrar sesión invalida el token. En este proyecto el cierre elimina la copia local: el backend no tiene un endpoint de revocación ni una lista de tokens revocados. Una copia externa de un JWT firmado puede seguir siendo válida hasta su caducidad. No se debe confundir borrado local con revocación en el servidor.
- Keystore/Keychain corresponden a Android/iOS. Brave usa la implementación web del paquete y no acredita por sí solo la protección de un dispositivo Android. Se verificó persistencia funcional en localhost; no se presenta como una prueba de Android Keystore.
- La transcripción automática confunde algunos términos (Hive, Dio y códigos HTTP). Se contrastaron con las diapositivas y el código, sin trasladar esos errores al proyecto.

## Respuestas a los ejercicios de las diapositivas

**Diapositiva 10, taquilla y pulsera:** la taquilla representa el endpoint de autenticación, que comprueba las credenciales y emite el JWT; la pulsera representa ese token presentado en las peticiones posteriores.

**Diapositiva 16, formulario bancario:** enviar JSON cuando el login exige `application/x-www-form-urlencoded` con `username` y `password` produce 422, aunque los datos sean correctos. Se corrige el formato, no la contraseña.

**Diapositiva 24, mensajes humanos:** es mejor explicar el problema y la acción posible. Ejemplo: «No hay conexión con el servidor. Revisa tu conexión e inténtalo de nuevo», evitando mostrar una traza DioException.

**Diapositiva 32, elemento eliminado por otra persona:** actualizar la lista consultando al servidor. Si una edición o eliminación recibe 404, retirar la información desactualizada mediante esa recarga y mostrar el mensaje correspondiente.

## Reflexión del instructivo

1. Otro teléfono no lee la caja Hive de este dispositivo; con una sesión válida sí puede consultar al servidor los gastos de su propia cuenta.
2. El servidor separa y autoriza los gastos usando la identidad del JWT. Las cajas por usuario separan la copia local, pero no sustituyen la autorización.
3. El token no va a Hive porque la caché no está cifrada. Se utiliza flutter_secure_storage; la contraseña no se guarda para restaurar la sesión.

El mini-reto de mostrar el correo bajo el título Gastos ya está implementado. El cambio de contraseña es opcional y permanece desactivado. La Tarea 3 de resumen por categoría se menciona como extensión para otra entrega; no se confundió con el CRUD obligatorio de esta actividad.

## Ejecutar en Windows

Abrir el workspace en Antigravity. En una terminal, desde `exploraec-curso/proyecto-curso-spec-kit`, configurar `.env` localmente a partir de `.env.example`, sin publicar secretos, y ejecutar:

```powershell
docker compose up --build -d
Invoke-RestMethod http://localhost:8000/health
```

Desde `exploraec-curso/exploraec`:

```powershell
flutter pub get
flutter test
flutter run -d web-server --web-port 8186 --dart-define=API_BASE_URL=http://localhost:8000
```

Abrir http://localhost:8186/ en Brave. Para probar la opción «Invalidar token (solo práctica)» debe utilizarse una ejecución debug; esa opción no aparece en release.

El backend fuente permite CORS para localhost:8186 y GET/POST/PATCH/DELETE/PUT/OPTIONS. Si una imagen antigua solo permite GET y POST, es necesario reconstruirla con el comando anterior. El informe detalla el intermediario temporal usado durante esta comprobación porque el entorno no tenía acceso al canal Docker para reconstruir la imagen.

Fuentes: diapositivas `sesion-08.pdf`, instructivo de la práctica 8 y [video de Moodle](https://cursos.cedia.org.ec/mod/page/view.php?id=49592). No se realizó entrega ni se marcaron recursos como completados en Moodle.
