# Reglas de diseño para dashboards internos

Un dashboard interno se usa todos los días, muchas horas, para encontrar algo y actuar. Se juzga por la rapidez para entender y decidir, no por lo lindo en una captura.

## Color
- **Verde, amarillo/naranja y rojo están reservados para estados** (ok / atención / problema). No se usan para decorar, para botones primarios ni para gráficos sin significado.
- **Un estado nunca se comunica solo con color.** Va color + ícono + texto ("Vencido", "Stock bajo"). Una persona daltónica tiene que poder leerlo.
- Un color de marca para acciones principales y foco. El resto en grises. Menos colores = más fácil ver lo importante.
- Contraste mínimo 4.5:1 para texto y 3:1 para bordes de inputs e íconos que transmiten información.

## Jerarquía y lectura
- Cada pantalla responde **una pregunta principal** ("¿qué pedidos están atrasados?"). Lo que responde esa pregunta va arriba y más grande.
- **Orden por severidad:** lo que requiere acción primero (rojo → amarillo → resto). Lo urgente no se pagina ni se colapsa.
- KPIs: 3 a 7 por pantalla, cada uno con su comparación (vs. ayer, vs. mes anterior) y un link a la lista que lo explica. Un número sin contexto no sirve.
- No inventar scores, rankings o porcentajes que el negocio no usa.

## Densidad y espaciado
- Dashboards internos aceptan más densidad que una web pública: filas de tabla de 40–48 px, texto de 13–14 px en tablas.
- Usar una escala de espaciado fija (4/8/12/16/24/32). Nada de valores sueltos.
- Alinear números a la derecha y con `font-variant-numeric: tabular-nums` para que se puedan comparar.

## Tablas
- `<table>` semántica, encabezado fijo al scrollear, columna clave (nombre/ID) fija si hay scroll horizontal.
- Orden por columna con indicador visible. Filtros visibles y fáciles de limpiar (chips con ✕ y "Limpiar todo").
- Acciones de fila a la derecha o en menú `⋯`. Acciones masivas aparecen recién al seleccionar.
- En celular: la tabla se transforma en lista de cards o tiene scroll horizontal con la primera columna fija. Nunca se corta.

## Estados obligatorios en toda vista con datos
1. **Cargando:** skeleton con la forma del contenido.
2. **Vacío:** explica por qué está vacío y ofrece la acción ("No hay pedidos con estos filtros · Limpiar filtros").
3. **Error:** qué pasó en lenguaje humano + "Reintentar".
4. **Sin permiso:** dice que no tiene acceso, no muestra una pantalla rota.

## Navegación
- Sidebar con secciones agrupadas por tarea, no por tabla de base de datos. Ítem activo claro.
- Breadcrumb o título que diga dónde está. El botón atrás del navegador tiene que funcionar (filtros y pestañas en la URL).
- Atajos de teclado para lo más usado si el usuario pasa horas en el sistema (buscar con `/` o `Ctrl+K`).

## Movimiento
- Solo para dar feedback (se guardó, se abrió un panel). 150–250 ms. Nada de animaciones de entrada en cada carga.
- Respetar `prefers-reduced-motion`.

## Señales de que algo "se ve hecho por IA" o genérico (evitar)
- Todo centrado, cards con sombra grande y bordes muy redondeados en todos lados, degradés violetas, íconos decorativos en cada título, mismo peso para todo.
