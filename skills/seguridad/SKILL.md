---
name: seguridad
description: Revisa y mejora la seguridad de un dashboard o sistema interno. Detecta el stack, clasifica el sistema en un nivel de riesgo (S1 básico, S2 con cuentas, S3 con datos sensibles), recorre el checklist de ese nivel contra el código real, entrega hallazgos ordenados por riesgo con archivo y línea, y los corrige uno por uno solo con aprobación. Usar cuando el usuario dice "revisá la seguridad", "es seguro esto?", "quiero mejorar la seguridad", "auditá el login/los permisos/los roles", "antes de lanzar", "me preocupa que alguien vea datos que no debe", o escribe /dashboard:seguridad.
---

# Seguridad de dashboard — diagnóstico real, arreglos aprobados

Esta skill no recita OWASP en abstracto: lee el código del sistema, decide qué nivel de seguridad necesita, verifica cada control **en el código** y reporta qué falta con archivo y línea. Arregla solo lo que el usuario aprueba.

## Reglas que no se rompen

1. **Cada hallazgo tiene evidencia:** archivo:línea o comando que lo demuestra. Sin evidencia no es hallazgo, es sospecha, y se reporta como tal.
2. **Nunca imprimir secretos.** Si se encuentra una clave, se dice dónde está y qué tipo es, nunca su valor. No leer `.env` reales salvo para listar nombres de variables.
3. **No tocar producción.** Nada de probar ataques contra el sistema en vivo, ni migraciones, ni cambios de configuración en servicios externos. Todo se prueba local.
4. **Arreglos con aprobación**, de a uno o por grupo, empezando por los críticos. Cada arreglo con su prueba (test o pasos para verificar).
5. **Si el usuario elige una opción más simple que la ideal**, no se veta: se implementa segura con controles compensatorios y se anota el riesgo aceptado en `docs/dashboard/DECISIONES.md`.

## Paso 0 — Anclar en el sistema

1. Leer `docs/dashboard/SISTEMA.md` si existe; si no, crearlo con el formato de la skill `diseno` (Paso 0). Para esta skill importan sobre todo: Stack, Datos, Usuarios y permisos.
2. Leer `docs/dashboard/DECISIONES.md` y `docs/dashboard/SEGURIDAD.md` si existen (riesgos ya aceptados no se vuelven a reportar como nuevos; se listan aparte).
3. Ubicar en el código: dónde se hace el login, dónde se valida la sesión en cada request, dónde se decide qué puede ver cada rol, cómo se accede a la base (ORM, SQL crudo, cliente desde el navegador), dónde se leen las variables de entorno, qué endpoints/acciones del servidor existen.

## Paso 1 — Clasificar el nivel

Leer `references/niveles.md` y decidir. Preguntar al usuario solo lo que el código no dice (ej: "¿se cargan DNIs, datos de salud, datos de tarjetas, sueldos?").

- **S1 — Básico:** todo sistema. Sin esto no se publica.
- **S2 — Cuentas:** hay usuarios con login y roles distintos (casi todo dashboard interno).
- **S3 — Datos sensibles:** salud, documentos de identidad, datos financieros, datos de menores, o lo que la ley local trate como sensible.

Los niveles se acumulan: S3 = S1 + S2 + S3.

## Paso 2 — Recorrer el checklist contra el código

Para cada control del nivel (`references/niveles.md`), marcar uno de:
- ✅ **Cumple** — con archivo:línea.
- ❌ **Falta** — con archivo:línea donde debería estar, o "no existe".
- ⚠️ **Parcial** — qué hay y qué falta.
- ❓ **No verificable desde el código** — ej: configuración del hosting. Se pregunta.

Consultar `references/por-stack.md` para los puntos específicos del stack detectado (Next.js, Supabase, Laravel, Django, Express, etc.).

Herramientas útiles si están disponibles:
- `/security-review` (viene con Claude Code) sobre los cambios pendientes.
- `npm audit --omit=dev` / `pnpm audit` / `composer audit` / `pip-audit` para dependencias con vulnerabilidades conocidas.
- Buscar secretos en el repo y el historial: `git log -p | grep -iE "(api[_-]?key|secret|password|token)\s*[:=]"` (reportar ubicación, nunca valor).

## Paso 3 — Reportar

Ordenado por riesgo. Para cada hallazgo:

```
[CRÍTICO | ALTO | MEDIO | BAJO] <título en lenguaje simple>
Dónde: <archivo:línea>
Qué puede pasar: <escenario concreto: "un usuario con rol vendedor puede ver los sueldos cambiando el id en la URL">
Arreglo: <qué se cambia, en 1-2 líneas>
Esfuerzo: bajo / medio / alto
```

Criterio de riesgo:
- **CRÍTICO:** cualquiera sin login o cualquier usuario puede leer/modificar datos de otros, o hay un secreto expuesto.
- **ALTO:** un usuario logueado puede hacer algo fuera de su rol; falta límite de intentos en login; datos sensibles sin protección.
- **MEDIO:** falta auditoría, encabezados de seguridad, validación de entradas en lugares no críticos.
- **BAJO:** mejoras de defensa en profundidad.

Cerrar con un resumen de una línea ("2 críticos, 3 altos, 5 medios") y la propuesta de orden de arreglo. **Esperar aprobación.**

## Paso 4 — Arreglar

- Uno por uno o por grupo aprobado, críticos primero.
- **La autorización va en el servidor, en una capa única** (un `guard`/middleware/policy que todas las acciones usan), nunca solo escondiendo botones en la UI.
- Cada arreglo lleva su verificación: un test que falla antes y pasa después, o los pasos manuales exactos ("logueate como vendedor, entrá a /sueldos/5 → tiene que dar 403").
- Si un arreglo necesita algo externo (rotar una clave en el proveedor, activar MFA en el hosting), se deja como tarea del usuario con los pasos exactos.

## Paso 5 — Dejar registro

Crear o actualizar `docs/dashboard/SEGURIDAD.md`:

```markdown
# Seguridad — <sistema>
Nivel: S1 + S2 (+ S3) — por qué
Última revisión: <fecha>

## Controles
| Control | Estado | Dónde |

## Riesgos aceptados
| Riesgo | Por qué se aceptó | Control compensatorio | Fecha |

## Pendientes
| Hallazgo | Riesgo | Bloqueado por |
```

Y una entrada en `docs/dashboard/DECISIONES.md` por cada decisión tomada.

## Reporte final al usuario

- **Hecho:** qué se arregló, en lenguaje simple.
- **Falta:** qué queda abierto y por qué.
- **Sigue:** el próximo arreglo y cómo probarlo.
- **De vos:** lo que tiene que hacer él (rotar una clave, decidir un riesgo), con la frase que puede responder.
