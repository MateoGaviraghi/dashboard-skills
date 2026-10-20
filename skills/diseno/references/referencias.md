# Dónde buscar referencias de dashboard

Abrir cada link antes de presentarlo. Si no carga o no muestra el componente, se descarta. Preferir productos reales en uso sobre shots conceptuales.

## Galerías de pantallas de productos reales (lo mejor)
| Fuente | Para qué | Nota |
|---|---|---|
| https://mobbin.com (filtro Web) | Pantallas reales de SaaS por patrón: tablas, filtros, settings, empty states | Parte requiere cuenta; si no hay acceso, usar las otras |
| https://refero.design | Igual que Mobbin, buscable por patrón y componente | Ídem |
| https://nicelydone.club | Flujos y componentes de SaaS (filtros, tablas, onboarding) | |
| https://saasframe.io | Pantallas de SaaS por categoría | |

## Componentes listos para copiar (cuando el sistema usa React/Tailwind)
| Fuente | Para qué |
|---|---|
| https://ui.shadcn.com/blocks | Bloques de dashboard, sidebar, login, tablas |
| https://ui.shadcn.com/examples/tasks | Tabla con filtros, orden, paginación y selección: referencia base de "data table" |
| https://tailwindcss.com/plus/ui-blocks/application-ui | Application UI: shells, tablas, listas, formularios (parte paga) |
| https://blocks.tremor.so | Bloques de dashboard: KPIs, gráficos, tablas |
| https://tanstack.com/table | Ejemplos de tablas: orden, filtro por columna, paginación, columnas fijas |

Si el sistema usa otra librería (MUI, Ant Design, Mantine, Chakra, Bootstrap, Filament, Django admin), buscar primero en la documentación oficial de esa librería: el componente nativo bien usado casi siempre le gana a uno importado.

## Productos para mirar en vivo (estándar alto de UI interna)
- **Linear**: tablas densas, filtros como chips, atajos de teclado, vista lista/tablero.
- **Stripe Dashboard**: tablas con detalle lateral, filtros guardados, estados vacíos claros.
- **Vercel Dashboard**: jerarquía limpia, logs con filtros, estados de carga.
- **PostHog / Plausible** (demo pública de Plausible: https://plausible.io/plausible.io): KPIs + gráficos + filtros de rango de fechas.
- **Supabase Studio**: editor de tablas, filtros por columna.

## Búsqueda general (último recurso)
- https://dribbble.com/search/dashboard-table (o el componente puntual): sirve para ideas visuales, pero muchos shots no son usables (datos falsos, sin estados, sin pensar en celular). Nunca más de 1 de 7 referencias puede venir de acá.

## Cómo buscar bien
- Buscar el **componente**, no "dashboard bonito": "data table filters chips", "sidebar collapsible app", "KPI cards", "empty state table", "date range picker dashboard", "bulk actions table".
- Para cada referencia anotar qué parte concreta se toma. Una referencia vale por una idea, no por toda la pantalla.
