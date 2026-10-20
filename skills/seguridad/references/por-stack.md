# Puntos específicos por stack

Leer solo la sección del stack detectado. Si el stack no está acá, aplicar `niveles.md` y buscar en la documentación oficial de seguridad del framework.

## Next.js (App Router)
- **Server Actions y Route Handlers son endpoints públicos.** Cada uno verifica sesión y rol adentro, aunque la página que lo usa ya esté protegida.
- El middleware (`middleware.ts`, o `proxy.ts` en Next 16+) sirve para redirigir, **no como única autorización**: la verificación real va en la capa de datos (DAL) o en cada acción.
- Patrón recomendado: un `src/lib/auth/guard.ts` (o similar) con `requireUser()` y `requireRole('admin')` que todas las acciones y consultas llaman.
- Nada de datos sensibles en props de Client Components que el usuario no deba ver: lo que se pasa al cliente se puede leer en el HTML.
- Variables `NEXT_PUBLIC_*` son públicas.
- Encabezados de seguridad en `next.config.*` (`headers()`) o en el hosting.

## Supabase
- Si el navegador habla directo con Supabase (cliente con `anon key`): **RLS activo en todas las tablas** y políticas por usuario/rol. Una tabla sin RLS es pública.
- La `service_role key` jamás en el cliente ni en variables públicas.
- Revisar con el linter de seguridad del dashboard de Supabase (Advisors) y corregir lo que marque.
- Funciones `security definer` revisadas: corren con permisos del dueño.

## Firebase
- Security Rules de Firestore/Storage: nada de `allow read, write: if true`. Reglas por `request.auth.uid` y rol.

## Laravel
- Policies/Gates para autorización; `$this->authorize()` en cada acción de controller.
- `$fillable` definido en cada modelo (evita mass assignment).
- `APP_DEBUG=false` en producción.
- Rate limiting con `RateLimiter::for` en login y API.
- `composer audit`.

## Django
- `DEBUG=False`, `ALLOWED_HOSTS` definido, `SECRET_KEY` fuera del repo.
- `@login_required` / `LoginRequiredMixin` y permisos por vista; en DRF, `permission_classes` en cada ViewSet y filtrado de queryset por usuario.
- `python manage.py check --deploy` y resolver sus avisos.
- `pip-audit`.

## Express / Node API
- `helmet` para encabezados, `express-rate-limit` en login y API.
- Middleware de auth en el router, no en cada handler suelto; verificar que ninguna ruta quedó afuera.
- Validación con Zod/Joi en cada body/query/params.
- CORS restringido a los orígenes del dashboard, nunca `*` con credenciales.

## NestJS
- Guards globales (`APP_GUARD`) con decorador `@Public()` explícito para excepciones: más seguro que proteger ruta por ruta.
- `ValidationPipe` global con `whitelist: true, forbidNonWhitelisted: true`.

## Prisma / Drizzle / TypeORM
- `$queryRaw` / `sql` crudo solo con parámetros (template tags del ORM), nunca con texto concatenado.
- Cada `findMany`/`select` sobre datos de clientes filtra por el dueño/empresa del usuario logueado.
