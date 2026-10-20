# Filtros, búsqueda y ordenamiento

## Dónde se filtra
| Filas en la tabla | Dónde filtrar | Por qué |
|---|---|---|
| < 1.000 y no crece rápido | Se puede traer todo (acotado) y filtrar en el navegador | Respuesta instantánea, simple |
| ≥ 1.000 o crece | **En el servidor**, en la consulta SQL | El navegador no debería recibir miles de filas |
| Lo que sea con permisos por fila | **Siempre en el servidor** | Filtrar en el cliente = los datos ya viajaron |

## Filtros en la URL
- Formato: `/pedidos?estado=pendiente&desde=2026-01-01&hasta=2026-01-31&orden=-fecha&pagina=2`
- Validar con un esquema (Zod, etc.) al leer: valor desconocido → default. Nunca un error 500 por una URL editada a mano.
- Al cambiar un filtro, volver a la página 1.
- Filtros de rango de fechas con atajos: Hoy · 7 días · 30 días · Este mes · Personalizado.
- Filtros por defecto sensatos (ej: "últimos 30 días", "solo activos") para que la primera carga sea liviana. El filtro por defecto se ve como chip activo, no escondido.
- Mostrar filtros activos como chips con ✕ y un "Limpiar todo". Mostrar el total de resultados ("124 pedidos").
- Filtros guardados ("Mis vistas") solo si el usuario repite las mismas combinaciones todos los días.

## Búsqueda
- Debounce de 250–300 ms al tipear; cancelar la búsqueda anterior si llega una nueva.
- Mínimo 2 caracteres antes de buscar en el servidor.
- Ignorar mayúsculas y acentos: en Postgres, `unaccent` + `ILIKE`, o columna normalizada.
- Según tamaño:
  - **Prefijo** (`nombre ILIKE 'jua%'`): rápido con índice `btree (lower(nombre) text_pattern_ops)` en Postgres.
  - **Contiene** (`ILIKE '%jua%'`) sobre tablas grandes: índice trigram (`pg_trgm` + `gin (nombre gin_trgm_ops)`) en Postgres; en MySQL, FULLTEXT.
  - **Texto libre en varios campos**: full-text search (Postgres `tsvector` + índice GIN) o un buscador dedicado (Meilisearch, Typesense) si hay cientos de miles de filas y tolerancia a errores de tipeo.
- Datos sensibles (DNI, email, teléfono) **no** van en la URL.
- Búsqueda global (`Ctrl+K`) solo si el sistema tiene muchas entidades distintas; lleva a la ficha, no a una lista.

## Ordenamiento
- Orden por defecto explícito (normalmente lo más reciente o lo más urgente primero).
- Orden en el servidor cuando se pagina en el servidor (ordenar una página sola en el navegador es un bug).
- Orden siempre estable: agregar el id como desempate (`ORDER BY fecha DESC, id DESC`), si no las filas saltan entre páginas.
- Solo permitir ordenar por columnas de una lista blanca (evita inyección y ordenar por columnas sin índice).
