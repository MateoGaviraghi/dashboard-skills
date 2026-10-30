---
name: diseno
description: Mejora el diseño de un dashboard o sistema interno pantalla por pantalla. Analiza el sistema, pregunta qué no le gusta al usuario, busca 5 a 7 referencias reales del componente exacto (con link), el usuario elige, se arma un plan, se aprueba, se construye y se verifica en 3 tamaños. Usar cuando el usuario dice "no me gusta esta pantalla", "mejorá el diseño", "quiero que la tabla se vea mejor", "buscame referencias", "ideas para el dashboard", "rediseñá el sidebar/los filtros/las cards", o escribe /dashboard:diseno.
---

# Diseño de dashboard — referencias primero, código después

Esta skill mejora la interfaz de un sistema interno (dashboard, backoffice, panel admin). El método es siempre el mismo: **entender el sistema → nombrar el componente → referencias reales → el usuario elige → plan aprobado → construir → mirar el resultado → corregir**. Nunca se inventa un diseño "de memoria" ni se rediseña todo de una vez.

## Reglas que no se rompen

1. **Una pieza por vez.** El usuario nombra la pantalla o el componente (tabla de pedidos, filtros, sidebar, cards de KPIs). No se toca lo que no nombró.
2. **Referencias antes que propuestas.** Primero 5 a 7 ejemplos reales con link; después el plan. Nada de "te propongo esto" sin haber mostrado de dónde sale.
3. **La referencia elegida se respeta.** Si elige la #3, se construye la #3 adaptada a su sistema, no una versión "inspirada" que se parece a otra cosa.
4. **Se construye en el código real del sistema**, con sus componentes, su librería de UI y sus estilos. Nada de mockups ni HTML sueltos.
5. **Plan aprobado antes de tocar archivos.** Tabla de archivos → qué cambia, y se espera un "dale" / "ok" explícito.
6. **Se mira antes de reportar.** El que construye revisa el resultado a 375, 1440 y 1920 px de ancho (ver Paso 7).
7. **Sin datos inventados.** Si falta un dato real (un texto, un número, un nombre), se marca `{{CONFIRMAR}}` y se pregunta.
8. **Lo rechazado no vuelve.** Todo rechazo se anota en `docs/dashboard/DECISIONES.md` y no se vuelve a ofrecer.

## Paso 0 — Anclar en el sistema (siempre, antes de cualquier otra cosa)

1. Si existe `docs/dashboard/SISTEMA.md`, leerlo entero. Si no existe, crearlo (formato abajo) leyendo el repo: `package.json` / `composer.json` / `requirements.txt` / `pyproject.toml`, la carpeta de rutas o páginas, el layout principal, la librería de UI (shadcn, MUI, Ant, Chakra, Bootstrap, Tailwind puro, etc.), el archivo de estilos/tokens (colores, tipografía, espaciado).
2. Si existe `docs/dashboard/DECISIONES.md`, leerlo: ahí está lo que ya se decidió y lo que se rechazó.
3. Levantar el sistema localmente (el comando de dev del proyecto) y abrir la pantalla en cuestión. Si no se puede levantar, decirlo y pedir captura.

Formato de `docs/dashboard/SISTEMA.md` (compartido con las skills `seguridad` y `datos`; si ya existe, solo completar lo que falte):

```markdown
# Sistema — <nombre>
> Escrito por las skills de dashboard. Fuente: el código. Si esto y el código no coinciden, gana el código.
Última revisión: <fecha>

## Stack
Frontend · Backend · Base de datos · Auth · UI kit · Hosting — una línea cada uno, con versión.

## Pantallas
| Ruta | Para qué sirve | Quién la usa | Datos que muestra |

## Diseño actual
Colores, tipografía, espaciado, componentes base (dónde están). Lo que se repite y lo que está inconsistente.

## Datos
Tablas principales, cuántas filas aprox., cómo se consultan (ORM, SQL, API).

## Usuarios y permisos
Roles que existen y cómo se controla qué ve cada uno.
```

## Paso 1 — Que el usuario nombre qué no le gusta

Preguntar en una sola tanda (no de a una):
- ¿Qué pantalla o componente? (mostrarle la lista de pantallas de `SISTEMA.md` si duda)
- ¿Qué le molesta? En sus palabras. Traducir con `references/correcciones.md` ("se ve amontonado" → densidad/espaciado; "no encuentro nada" → jerarquía/filtros; "se ve viejo" → tipografía, bordes, sombras, color).
- ¿Hay algún sistema que use y le guste cómo se ve? (es la mejor pista de gusto).

Si el usuario dice "todo", proponer un orden: primero el layout general (sidebar + header), después la pantalla más usada, después el resto. Una pieza por vez.

## Paso 2 — Investigar 5 a 7 referencias del componente exacto

- Buscar ejemplos **del mismo componente** (si es una tabla con filtros, tablas con filtros; no dashboards enteros).
- Fuentes en `references/referencias.md`. Priorizar productos reales en producción por sobre shots de Dribbble.
- **Abrir cada link y verificar que funciona y muestra lo que se dice.** Link roto o que no muestra el componente = se descarta.
- Variedad real: no 7 versiones de lo mismo. Mezclar densidades y enfoques (ej: tabla densa tipo Linear, tabla con cards tipo Stripe, tabla con panel lateral de detalle).
- Pasar cada candidata por `references/reglas-dashboard.md`: si rompe una regla (ej: usa rojo como decoración), se descarta o se aclara qué se ajustaría.
- **No quedarse en la primera idea.** Entre las 5 a 7 tiene que haber al menos 3 enfoques de verdad distintos (densidad, estructura, cómo se muestra el dato), no variaciones de color de la misma pantalla.
- Si está instalada la skill `design-taste-frontend` (repo `leonxlnx/taste-skill`), leer su `references/app-screens.md` y pasar cada candidata también por esas reglas (color en el dato y no en el marco, KPIs como tipografía y no como tarjetas con ícono, superficies planas, tablas de líneas y alineación). Para una pantalla nueva desde cero, su `scripts/draw.mjs --app` reparte direcciones para elegir.

## Paso 3 — Presentar

Para cada referencia, numerada:

```
#N — <nombre del producto / patrón>
Link: <url directa>
Qué toma de ahí: <1 línea concreta: "filtros como chips arriba de la tabla, se ven los activos de un vistazo">
Cómo quedaría en tu sistema: <1 línea>
Esfuerzo: bajo / medio / alto
```

Cerrar con **→ Mi recomendación: #N** y por qué, en una línea ligada a lo que el usuario dijo que le molestaba. Si hay herramienta de capturas disponible, mostrar captura.

## Paso 4 — Elegir

- Elige un número → Paso 5.
- Elige una mezcla ("los filtros de la 2 con la tabla de la 5") → construir 2 variantes reales en una ruta temporal o detrás de un parámetro (`?variante=a|b`) para que compare en su sistema, y elige. Después se borra la que perdió.
- Rechaza todas → preguntar qué no le gustó de cada una, anotar en `DECISIONES.md`, nueva ronda de referencias.

## Paso 5 — Plan y aprobación

Tabla:

| Archivo | Qué cambia | Por qué |
|---|---|---|

Más: componentes nuevos que hacen falta (y si la librería de UI ya los tiene), qué pasa en celular, estados que se cubren (vacío, cargando, error, sin permisos). **Esperar el "dale".**

## Paso 6 — Construir

- Reusar componentes y tokens existentes. Si hay que crear un color o tamaño nuevo, se agrega como token, no hardcodeado.
- Cubrir siempre los cuatro estados: **cargando** (skeleton con la forma real, no spinner suelto), **vacío** (qué hacer ahora, con acción), **error** (qué pasó y cómo reintentar), **con datos**.
- Accesibilidad mínima: contraste ≥ 4.5:1 en texto, foco visible, todo usable con teclado, tablas con `<table>` semántica, íconos con etiqueta.
- Si el cambio afecta cómo se cargan los datos (filtros, paginación, búsqueda), aplicar también la skill `datos`.

## Paso 7 — Verificar (el que construye, antes de reportar)

Abrir la pantalla a **375 px** (celular), **1440 px** (notebook) y **1920 px** (monitor). Revisar: nada se corta ni se superpone, la tabla tiene scroll horizontal o se transforma en celular, los estados vacío/cargando/error se ven, el foco de teclado se ve, no hay errores en consola. Si hay herramienta de navegador o Playwright disponible, sacar las 3 capturas. Si algo falla, se arregla antes de reportar.

Después, dos pruebas más:
- **Peor caso de datos.** Cargar la pantalla con lo que de verdad aparece en un sistema: un nombre de 60 caracteres, un email sin espacios, un nombre de una letra, campos vacíos, cero filas, 10.000 filas, montos enormes y negativos, texto no latino, emoji. Nada se corta sin aviso, nada empuja el layout, cero filas muestra el estado vacío. Si está instalada la skill `break-ui` (repo `emilkowalski/skills`), usarla.
- **Crítica de jurado.** Mirar las capturas como lo haría un diseñador senior que no la hizo: ¿se entiende en 5 segundos qué es lo importante?, ¿hay algo que esté solo para decorar?, ¿el dato manda sobre el marco? Si está instalada `design-taste-frontend`, correr su `scripts/inspect.mjs --app` sobre la pantalla. Lo que no pasa se arregla antes de reportar.

## Paso 8 — Reportar

Cuatro bloques, en lenguaje simple:
- **Hecho:** qué cambió y dónde verlo (ruta exacta).
- **Falta:** lo que no se hizo o no se pudo verificar, y por qué.
- **Sigue:** la próxima pieza sugerida.
- **De vos:** lo que el usuario tiene que decidir, con la frase que puede responder ("ok seguimos con los filtros").

## Paso 9 — Correcciones y cierre

- Leer las correcciones con `references/correcciones.md`. Si no está claro, preguntar con dos opciones concretas, no con "¿qué querés decir?".
- Al cerrar cada pieza, agregar a `docs/dashboard/DECISIONES.md`:

```markdown
## D-NNN — <fecha> — <componente>
Elegido: #N <referencia> (<link>)
Rechazado: <lo que no y por qué, en palabras del usuario>
Archivos: <lista>
```

- Commit solo si el usuario lo pide.
