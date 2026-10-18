# Estado y verificación de la práctica 8

Actualizado: 18 de octubre de 2026. Se conservó el proyecto existente y se comparó con el video, las diapositivas y el instructivo. El código fuente de lib/ y test/ coincide con el repositorio público ya publicado; no fue necesario cambiarlo para completar las funciones obligatorias.

## Implementación

Dio con interceptores de Bearer y 401; AuthController; registro y login con validación; flutter_secure_storage; sesión restaurada; CRUD de gastos; categorías del servidor; PATCH de los campos modificados; recarga de la lista y de la caché; eliminación con confirmación; mensajes de negocio; limpieza de sesión y caja Hive. El correo bajo Gastos resuelve el mini-reto.

## Pruebas actuales

- 15/15 pruebas oficiales del curso aprobadas con `flutter test --no-pub --reporter expanded`.
- `dart analyze lib test`: No issues found.
- Compilación Flutter web release correcta. Se utilizó una copia local del SDK por las restricciones de acceso al SDK instalado.
- 17 comprobaciones de integración aprobadas contra la API real y PostgreSQL en Docker. El JSON adjunto contiene los resultados, sin tokens ni contraseñas.

| Comprobación real | Resultado | Evidencia |
| --- | --- | --- |
| Registro desde Brave | Cuenta de prueba creada e inicio automático; lista inicialmente vacía | sesion8-01-gasto-creado.png muestra la sesión resultante y el gasto posterior |
| Crear gasto desde Brave | Prueba sesion 8 - almuerzo, comida, 6.50 | sesion8-01-gasto-creado.png |
| Superar 500 al editar | Mensaje del servidor y formulario conservado con monto 501 | sesion8-02-limite-500.png |
| Editar monto | Cambia a 7.50 y actualiza la lista | sesion8-03-gasto-editado.png |
| Reiniciar aplicación | Recupera sesión y gasto sin solicitar otra vez las credenciales | sesion8-04-sesion-restaurada.png |
| Interrumpir la conexión a la API | Mantiene gasto y fecha de sincronización en el aviso de respaldo | sesion8-05-sin-conexion.png |
| Restablecer conexión y Reintentar | Recupera la lista del servidor y elimina el aviso | Comprobación visible en Brave |
| Confirmación de eliminación | Muestra nombre y advertencia; Cancelar conserva el gasto | sesion8-06-confirmacion-eliminar.png |
| CRUD, errores y autorización por API | POST 201, PATCH 200, DELETE 204, segundo DELETE 404, 400 por límite, 401 por credenciales/token, 422 por formato y separación entre cuentas | sesion8-validacion-api-real.json |

Las evidencias anteriores del desarrollo inicial se conservan por separado. El gasto de demostración de Brave se mantiene en la cuenta de prueba. Las comprobaciones directas de API crearon otras cuentas aisladas y eliminaron únicamente su propio gasto temporal; no modificaron los gastos anteriores del curso.

## Infraestructura y límites de esta comprobación

Docker Desktop se inició y los contenedores existentes se arrancaron desde su interfaz. /health respondió status ok y db ok. El acceso CLI al canal local Docker sigue denegado: no fue posible reconstruir la imagen desde este entorno.

La imagen existente tenía CORS antiguo (GET, POST, OPTIONS). El código fuente local y publicado ya contiene PATCH, DELETE y PUT. Para las pruebas en Brave se compiló con API_BASE_URL=http://localhost:8002 y se utilizó un intermediario temporal, limitado a loopback, que reenvía las peticiones al backend real localhost:8000 y permite el origen localhost:8186. No simula respuestas ni sustituye PostgreSQL. Las pruebas directas de API se ejecutaron contra localhost:8000, sin intermediario.

La desconexión se provocó deteniendo ese intermediario, no deteniendo PostgreSQL. Después se restableció. La ejecución directa sin intermediario requiere reconstruir la imagen desde el código actualizado. No se borraron volúmenes ni se cambiaron las protecciones del navegador.

Brave verifica la implementación web de flutter_secure_storage; no se comprobó Android Keystore ni el anexo adb. El cambio de contraseña es opcional y no se activó.

## Respuestas y entrega

Las respuestas a los ejercicios y el contraste con los materiales están en `Actividad y contraste - Sesion 8.md`. Esta revisión actualiza el repositorio de la práctica 8; no realiza una entrega en Moodle ni publica .env, claves, tokens o bases locales.

## Cierre de comprobación de sesión en Brave

Se compiló además en debug para disponer del menú de práctica. «Invalidar token» seguido de Recargar produjo un 401 real: la aplicación volvió a pedir inicio de sesión, mostró el aviso de caducidad y dejó de mostrar los gastos. Evidencia: `sesion8-07-sesion-caducada.png`. Los tres favoritos anteriores se conservaron después de este cierre: `sesion8-08-favoritos-conservados.png`. El cierre por caducidad utiliza el mismo cerrarSesion() que la acción manual; el borrado de la caja y la lista también está cubierto por la prueba oficial del Paso 8.

Tras reiniciar después del cierre por 401, Gastos volvió a mostrar únicamente el botón Iniciar sesión, sin restaurar la cuenta anterior.
