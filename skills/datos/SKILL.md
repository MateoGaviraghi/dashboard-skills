---
name: datos
description: Mejora cómo un dashboard o sistema interno maneja los datos - filtros, búsqueda, paginación, ordenamiento, velocidad (consultas lentas, índices, carga de pantallas), alertas, notificaciones y recomendaciones. Mide antes de tocar, propone reglas concretas con números, y aplica con aprobación. Usar cuando el usuario dice "es lento", "tarda en cargar", "quiero filtros", "agregá un buscador", "paginación", "carga todo de golpe", "quiero alertas cuando...", "avisame si...", "recomendaciones", "que el sistema me sugiera", o escribe /dashboard:datos.
---

# Datos de dashboard — filtros, búsqueda, paginación, velocidad, alertas

Esta skill trabaja la capa donde un dashboard se vuelve lento o inútil: qué datos trae, cuántos, cómo se filtran y cuándo avisa. Regla madre: **primero medir, después cambiar, después volver a medir.**

## Reglas que no se rompen

1. **Medir antes de optimizar.** Cada cambio de velocidad tiene un número antes y un número después (ms de la consulta, ms de la pantalla, filas traídas).
2. **Nunca traer una tabla entera.** Toda lista está acotada: por página, por rango de fechas o por un límite explícito.
3. **No se crea un índice sin una consulta real que lo use.** Cada índice se anota junto a la consulta que justifica.
4. **Filtros, orden y página viven en la URL** (se pueden compartir, el botón atrás funciona, recargar no los pierde). Un valor inválido en la URL cae al default, nunca rompe la pantalla.
5. **Datos sensibles nunca en la URL** (DNI, email, teléfono): esa búsqueda va por POST o estado local.
6. **Umbrales de alertas configurables**, no escritos en el código: un admin los cambia sin deploy.
7. **Cambios con aprobación.** Plan en tabla, "dale", después código.

## Paso 0 — Anclar en el sistema

1. Leer `docs/dashboard/SISTEMA.md` (crearlo si no existe con el formato de la skill `diseno`, Paso 0) y `docs/dashboard/DECISIONES.md`.
2. Ubicar: cómo se consultan los datos (ORM, SQL, API externa), dónde se cargan las listas, si la paginación/filtro se hace en el servidor o en el navegador, qué índices tiene la base (migraciones o `schema`).
3. Preguntar qué tema trabajar si no lo dijo: **filtros y búsqueda · paginación · velocidad · alertas y recomendaciones**. Se puede hacer uno o todos, en ese orden.

## Paso 1 — Diagnóstico (con números)

Para cada pantalla con datos de `SISTEMA.md`, completar:

| Pantalla | Filas en la tabla | Filas que trae | ¿Filtra/pagina en servidor? | Tiempo de carga | Consultas por carga |
|---|---|---|---|---|---|

Cómo medir:
- **Filas:** `SELECT count(*)` en la tabla (local o con permiso del usuario).
- **Tiempo de consulta:** `EXPLAIN ANALYZE` (Postgres/MySQL 8+) sobre la consulta real; buscar `Seq Scan` sobre tablas grandes, `rows` estimadas vs. reales, sorts en disco.
- **Consultas por carga:** activar el log de consultas del ORM y contar. Muchas consultas iguales con distinto id = problema N+1.
- **Pantalla:** pestaña Network del navegador (tamaño de la respuesta, tiempo hasta datos) y Lighthouse/Web Vitals para LCP e INP.

Señales típicas: lista que trae miles de filas y pagina en el navegador · N+1 · `SELECT *` con columnas pesadas · filtro por columna sin índice · búsqueda con `LIKE '%texto%'` sobre tabla grande · la pantalla espera todos los bloques para mostrar algo.

Reportar el diagnóstico ordenado por impacto y **esperar aprobación** del plan.

## Paso 2 — Aplicar por tema

Leer solo la referencia del tema que se trabaja:

| Tema | Referencia |
|---|---|
| Filtros, búsqueda, ordenamiento | `references/filtros-busqueda.md` |
| Paginación | `references/paginacion.md` |
| Velocidad (consultas, índices, carga de pantalla, caché) | `references/velocidad.md` |
| Alertas, notificaciones, recomendaciones | `references/alertas.md` |

Si el cambio toca cómo se ve (chips de filtros, paginador, panel de alertas), aplicar también la skill `diseno` para el componente visual.

## Paso 3 — Verificar

- Volver a medir lo mismo que en el Paso 1 y mostrar antes → después.
- Probar los bordes: lista vacía, un solo resultado, última página, filtro sin resultados, URL con valor inválido, búsqueda con acentos y mayúsculas, usuario sin permiso.
- Confirmar que los filtros respetan los permisos (un filtro no puede mostrar registros que el rol no puede ver).

## Paso 4 — Registrar y reportar

Agregar a `docs/dashboard/DECISIONES.md`:

```markdown
## D-NNN — <fecha> — <tema / pantalla>
Antes: <números>
Cambio: <qué se hizo>
Después: <números>
Índices nuevos: <índice → consulta que lo usa>
```

Reporte al usuario en cuatro bloques: **Hecho** (con antes → después), **Falta**, **Sigue**, **De vos** (con la frase que puede responder).
