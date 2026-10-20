# Niveles de seguridad y sus controles

Los niveles se acumulan. Un dashboard interno con login casi siempre es **S1 + S2**. Si guarda datos de salud, identidad, finanzas o menores, es **S1 + S2 + S3**.

## S1 — Básico (todo sistema)

| # | Control | Cómo se verifica en el código |
|---|---|---|
| S1-01 | HTTPS en todos lados; cookies con `Secure` | Config del hosting / framework; flags de cookies |
| S1-02 | Ningún secreto en el código ni en el repo (tampoco en el historial de git) | Buscar claves en archivos y `git log -p` |
| S1-03 | Secretos solo del lado del servidor; nada sensible en variables expuestas al navegador (`NEXT_PUBLIC_*`, `VITE_*`, `REACT_APP_*`) | Revisar variables con prefijo público |
| S1-04 | Dependencias sin vulnerabilidades críticas conocidas | `npm audit` / `composer audit` / `pip-audit` |
| S1-05 | Toda entrada del usuario validada en el servidor (tipo, largo, formato) con un esquema (Zod, Yup, FormRequest, serializers) | Cada endpoint/acción del servidor |
| S1-06 | Consultas a la base parametrizadas (ORM o placeholders), nunca concatenando texto del usuario | Buscar SQL armado con `+` o template strings |
| S1-07 | Errores al usuario sin detalles internos (stack traces, SQL) | Manejo de errores global |
| S1-08 | Encabezados de seguridad: `Content-Security-Policy`, `X-Frame-Options`/`frame-ancestors`, `X-Content-Type-Options`, `Referrer-Policy` | Config del framework o del hosting |
| S1-09 | Formularios públicos con límite de envíos (rate limit) | Endpoint de contacto, registro, etc. |
| S1-10 | Backups automáticos de la base y probados al menos una vez | Preguntar al usuario: ¿se restauró alguna vez? |

## S2 — Cuentas (hay login y roles)

| # | Control | Cómo se verifica |
|---|---|---|
| S2-01 | Login con librería probada (no auth casera); contraseñas hasheadas con bcrypt/argon2 | Librería de auth usada |
| S2-02 | Límite de intentos de login (ej: 5 por 15 min por IP y por usuario) | Middleware o tabla de intentos |
| S2-03 | Sesión verificada **en el servidor en cada request** a datos, no solo en el layout ni en el cliente | Cada endpoint/acción/loader |
| S2-04 | **Autorización por rol en una capa única del servidor** (guard/policy/middleware); la UI solo esconde, no protege | Buscar chequeos de rol; buscar endpoints sin chequeo |
| S2-05 | **Sin acceso a registros ajenos cambiando un id** (IDOR): cada consulta filtra por el dueño/empresa/permiso | Rutas tipo `/x/[id]` y acciones que reciben ids |
| S2-06 | Matriz rol × acción escrita y coincide con el código | `docs/dashboard/SEGURIDAD.md` vs. guards |
| S2-07 | Cookies de sesión `HttpOnly`, `Secure`, `SameSite=Lax` o `Strict`; expiración razonable (ej: 8–12 h inactivo) | Config de la librería de auth |
| S2-08 | Protección CSRF en mutaciones (la da el framework o `SameSite`) | Formularios y endpoints POST |
| S2-09 | Cerrar sesión invalida la sesión en el servidor | Logout |
| S2-10 | Recuperar contraseña con token de un solo uso y que expira | Flujo de reset |
| S2-11 | Límite de requests a mutaciones por usuario (ej: 120/min) | Middleware o tabla de rate limit |
| S2-12 | Si se usa Supabase/Firebase con acceso desde el navegador: reglas (RLS / Security Rules) activas en **todas** las tablas | Políticas de cada tabla |

## S3 — Datos sensibles

| # | Control | Cómo se verifica |
|---|---|---|
| S3-01 | MFA obligatorio para todos los usuarios del sistema (o al menos admins), aplicado por el servidor | Guard que exige MFA antes de dar acceso |
| S3-02 | **Auditoría append-only**: quién vio/creó/modificó/borró qué y cuándo; el usuario sale de la sesión del servidor, nunca del cliente | Tabla de auditoría y dónde se escribe |
| S3-03 | Exportaciones (Excel/CSV/PDF) solo para roles autorizados y registradas en auditoría con cantidad de filas | Endpoints de export |
| S3-04 | Mínimo dato necesario: cada pantalla trae solo las columnas que muestra | Selects con `*` sobre tablas sensibles |
| S3-05 | Datos sensibles no aparecen en URLs, logs ni mensajes de error (ej: buscar por DNI no va en la URL) | Query strings, logs |
| S3-06 | Base de datos sin acceso directo desde el navegador | Clientes de DB en código de cliente |
| S3-07 | Cifrado en reposo (lo da el proveedor de base gestionada; confirmar) y backups cifrados | Preguntar / docs del proveedor |
| S3-08 | Política de retención y borrado definida con el dueño del sistema; cumplimiento de la ley de datos local (en Argentina, Ley 25.326) | Preguntar; dejar como pendiente legal si no está |
| S3-09 | Sesiones más cortas (ej: 30 min inactivo) y cierre de sesiones de otros dispositivos | Config de auth |
