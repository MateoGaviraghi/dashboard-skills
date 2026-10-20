# Alertas, notificaciones y recomendaciones

## Primero: definir las reglas con el usuario
Una alerta útil responde "¿qué tengo que hacer ahora?". Antes de programar, armar con el usuario esta tabla (y guardarla en `docs/dashboard/ALERTAS.md`):

| Alerta | Condición exacta | Umbral (configurable) | Nivel | Quién la ve | Canal | Acción sugerida |
|---|---|---|---|---|---|---|
| Stock bajo | `stock <= minimo` | mínimo por producto, default 5 | Amarilla | Depósito, Admin | Panel | "Reponer" → orden de compra |
| Cliente inactivo | sin compra en N días | N = 30 | Amarilla | Ventas | Panel + resumen diario por mail | "Contactar" |
| Pago vencido | `vencimiento < hoy` | — | Roja | Admin | Panel + mail | "Ver deuda" |

Números concretos, no adjetivos ("pocos", "hace mucho"). Si el usuario no sabe el número, proponer uno y marcarlo para revisar en 2 semanas.

## Reglas
- **Umbrales en una tabla de configuración** (`config_alertas` o similar), editables desde una pantalla de admin. El código lee la configuración; no hay números fijos en el código.
- **El nivel lo calcula el servidor**, en una sola función por tipo de alerta (ej: `evaluarAlertas()`), no en cada pantalla.
- **Tres niveles como máximo**: Roja (actuar hoy) · Amarilla (atención) · Info. Siempre color + ícono + texto.
- **Orden por severidad.** Las rojas no se paginan ni se colapsan; las amarillas pueden colapsarse con contador.
- **Cada alerta lleva a la acción** (link al registro o botón), no solo informa.
- **Se pueden resolver o posponer** ("posponer 7 días"), y eso queda registrado (quién, cuándo).
- **Datos sin clasificar** (falta información para evaluar) se cuentan aparte; nunca se suman a "todo bien".
- **Anti-ruido:** si una alerta salta todos los días y nadie actúa, el umbral está mal o la alerta sobra. Revisar con el usuario.

## Cómo se calculan
| Opción | Cuándo |
|---|---|
| Al cargar la pantalla (consulta en vivo) | Pocos datos, condición simple, solo se ve en el panel |
| Job programado (cron cada X minutos u horario) que guarda en tabla `alertas` | Condiciones caras, o cuando hay que mandar mails/WhatsApp, o para historial |
| Al guardar (trigger o en la acción del servidor) | Cuando la alerta depende de un evento puntual ("se registró un pago mayor a X") |

Para cron: usar el del hosting o de la base (Vercel Cron, pg_cron, Laravel Scheduler, Celery beat, cron del servidor). Verificar que el método HTTP del cron coincida con el del endpoint y que el endpoint esté protegido con un secreto.

## Notificaciones
- Panel/campana dentro del sistema primero. Mail o WhatsApp solo para rojas o para un resumen diario (no un mail por evento).
- Cada usuario puede elegir qué recibe y por qué canal.
- Envíos idempotentes: si el job corre dos veces, no se manda dos veces (guardar "notificado_en").

## Recomendaciones
Empezar por reglas simples y explicables antes que IA:
- "Clientes que compraban todos los meses y hace 45 días que no" → contactar.
- "Productos que se agotan antes de fin de mes los últimos 3 meses" → subir el mínimo.
- "Pedidos que suelen atrasarse con este proveedor" → avisar al cargar.

Cada recomendación muestra **por qué** ("compró 6 de los últimos 6 meses, último pedido hace 47 días") y una acción. Si después de usarlas hace falta algo más sofisticado (predicción, IA), se evalúa con datos de qué recomendaciones se usaron y cuáles se ignoraron.
