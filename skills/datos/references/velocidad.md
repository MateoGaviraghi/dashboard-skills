# Velocidad

Siempre: medir → cambiar una cosa → volver a medir. Si no mejora, se revierte.

## Metas
| Qué | Meta |
|---|---|
| Consulta de una lista | < 100 ms en la base |
| Respuesta del servidor para una pantalla | < 500 ms |
| LCP (la pantalla muestra lo principal) | < 2,5 s, medido en el percentil 75 |
| INP (respuesta a clicks/tipeo) | < 200 ms |
| CLS (saltos de contenido) | < 0,1 |

## Base de datos (lo que más rinde)
1. **N+1**: una consulta por fila (ej: traer el cliente de cada pedido en un loop). Arreglo: join o `include`/`with`/`select_related`/`prefetch_related` del ORM, o una consulta con `WHERE id IN (...)`.
2. **`SELECT *`**: traer solo las columnas que la pantalla muestra. Columnas pesadas (JSON grandes, textos, archivos) solo en la ficha de detalle.
3. **Índices**: crear solo para consultas reales y lentas. Mirar `EXPLAIN ANALYZE`:
   - `Seq Scan` sobre tabla grande con filtro → índice en la columna del `WHERE`.
   - Filtro + orden → índice compuesto: columnas de igualdad primero, después la del orden (`(estado, fecha DESC)`).
   - Columnas con muy pocos valores distintos (booleanos) solas no sirven como índice; sí como parte de uno compuesto o índice parcial (`WHERE activo = true`).
   - Cada índice hace más lentas las escrituras: no indexar "por las dudas".
   - Anotar cada índice con la consulta que lo justifica en `DECISIONES.md`.
4. **Agregados caros** (totales del mes, KPIs sobre millones de filas): tabla resumen actualizada por un job, o vista materializada que se refresca cada X minutos. El dashboard lee el resumen.
5. **Pool de conexiones** configurado (especialmente en serverless: usar el pooler del proveedor).

## Servidor
- Verificar la sesión **una vez por request** y reusar el resultado, no en cada componente.
- Consultas independientes en paralelo (`Promise.all`), no una detrás de otra.
- Caché solo para datos que no son por usuario y cambian poco (catálogos, configuración), con invalidación al guardar. **Nunca cachear datos con permisos por usuario en una caché compartida.**
- Comprimir respuestas (gzip/brotli; casi siempre lo hace el hosting).

## Pantalla
- Mostrar el esqueleto de la pantalla (layout, títulos, filtros) **sin esperar los datos**; cada bloque de datos carga por su cuenta con skeleton (en React/Next: `Suspense` por bloque).
- Un bloque lento no puede trabar los demás.
- Tablas de muchas filas visibles a la vez (> 200): virtualizar (TanStack Virtual, react-window).
- Gráficos y librerías pesadas cargadas solo en la pantalla que las usa (import dinámico).
- Refresco automático (polling): cada 30–60 s como mínimo, pausado si la pestaña no está visible, y sin re-renderizar si los datos no cambiaron (comparar antes de actualizar el estado).
- Feedback inmediato al guardar (actualización optimista o estado de carga en el botón).
