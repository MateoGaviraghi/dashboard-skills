# Diccionario de correcciones

El usuario describe lo que siente, no la causa técnica. Esta tabla traduce. Si una frase no está acá, preguntar con dos opciones concretas ("¿es que hay mucho texto junto, o que no sabés dónde mirar primero?").

| Dice | Suele significar | Qué se toca |
|---|---|---|
| "Se ve amontonado" / "mucha cosa" | Poco aire o demasiada información al mismo nivel | Espaciado, agrupar en secciones, esconder lo secundario (pestañas, panel de detalle) |
| "Se ve vacío" / "pobre" | Poca densidad o falta de contraste de jerarquía | Subir densidad, títulos con más peso, mostrar datos útiles en vez de aire |
| "No encuentro nada" | Jerarquía o navegación mala | Orden por importancia, buscador visible, filtros, agrupar el sidebar por tarea |
| "Se ve viejo" | Tipografía, bordes, sombras y colores de otra época | Tipografía actual (Inter, Geist, IBM Plex, sistema), bordes de 1 px suaves, menos sombras, grises neutros |
| "Se ve barato" / "poco profesional" | Inconsistencia: tamaños, colores y espacios distintos en cada lado | Unificar tokens: una escala de espaciado, 2 pesos de fuente, 1 radio de borde |
| "Muy colorido" / "cansa la vista" | Color usado para decorar | Grises para todo, color solo para estados y acción principal |
| "Muy aburrido" / "frío" | Todo gris sin acento ni jerarquía | Un color de marca en acciones y foco, títulos con carácter, iconografía consistente |
| "Es lento" / "tarda" | Puede ser percepción (sin skeleton) o lentitud real | Primero medir. Si es real → skill `datos`. Si es percepción → skeletons y feedback inmediato |
| "No sé si se guardó" | Falta feedback | Toast o estado inline "Guardado", botón con estado de carga |
| "En el celu no se puede usar" | No responsive | Tabla → cards o scroll con columna fija, sidebar → menú, botones de 44 px |
| "Me pierdo" | No sabe dónde está | Título de página claro, ítem activo del sidebar, breadcrumb |
| "Más moderno" | Ambiguo | Preguntar: ¿qué sistema te parece moderno? Usar ese como referencia |
| "Así no" (sin más) | Rechazo sin causa | Mostrar 2 alternativas opuestas y preguntar cuál está más cerca |

Cada rechazo se anota en `docs/dashboard/DECISIONES.md` con las palabras del usuario.
