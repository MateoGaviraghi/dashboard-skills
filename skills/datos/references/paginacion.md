# Paginación

## Cuál usar
| Situación | Tipo | Cómo |
|---|---|---|
| Listas administrativas, hasta ~100.000 filas, el usuario quiere "ir a la página 5" | **Offset** | `LIMIT 25 OFFSET 100` + `count(*)` para el total |
| Tablas grandes o que crecen mucho (logs, movimientos, auditoría), feeds, scroll infinito | **Cursor (keyset)** | `WHERE (fecha, id) < (:ultimaFecha, :ultimoId) ORDER BY fecha DESC, id DESC LIMIT 25` |
| Menos de ~200 filas siempre | Sin paginar, lista completa | Pero con límite de seguridad (`LIMIT 500`) |

Por qué cursor en tablas grandes: `OFFSET 100000` obliga a la base a leer y descartar 100.000 filas; con cursor siempre lee solo las 25 que necesita, y no se saltean ni repiten filas si entran registros nuevos mientras se navega.

## Reglas
- Tamaño de página por defecto 25; opciones 25 / 50 / 100. Máximo permitido en el servidor: 100 (aunque la URL pida 10.000).
- Página en la URL (`?pagina=3` o `?cursor=...`). Página fuera de rango → última página válida o vacía con mensaje, nunca error.
- Orden estable con id de desempate (ver `filtros-busqueda.md`).
- El índice tiene que coincidir con el orden: para `ORDER BY fecha DESC, id DESC` → índice `(fecha DESC, id DESC)` (o con la columna de filtro adelante si siempre se filtra: `(empresa_id, fecha DESC, id DESC)`).
- `count(*)` exacto es caro en tablas enormes: mostrar "más de 10.000" o contar con límite (`SELECT count(*) FROM (SELECT 1 FROM t WHERE ... LIMIT 10001)`), o usar cursor sin total.
- Al cambiar filtros u orden → volver a la página 1.
- El paginador muestra: "1–25 de 1.240", anterior/siguiente, y números de página solo en offset.
- Scroll infinito solo en feeds; en tablas de trabajo, paginador (el usuario necesita volver a "donde estaba").
