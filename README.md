# dashboard-skills

Tres skills de Claude Code para mejorar un dashboard o sistema interno, trabajando sobre el código real del sistema.

| Comando | Qué hace |
|---|---|
| `/dashboard:diseno` | Revisa el sistema, pregunta qué no te gusta, busca 5 a 7 referencias reales del componente con link, elegís, aprueba el plan, construye y verifica en celular, notebook y monitor. |
| `/dashboard:seguridad` | Clasifica el sistema en un nivel (S1 básico, S2 cuentas, S3 datos sensibles), revisa cada control en el código y entrega hallazgos por riesgo con archivo y línea. Arregla solo lo que aprobás. |
| `/dashboard:datos` | Filtros, búsqueda, paginación, velocidad, alertas y recomendaciones. Mide antes y después de cada cambio. |

Las tres comparten una carpeta en tu repo, `docs/dashboard/`:
- `SISTEMA.md`: qué es tu sistema (stack, pantallas, datos, roles). Lo escribe la primera skill que corras.
- `DECISIONES.md`: lo que se decidió, lo rechazado y por qué. Es la memoria entre chats.
- `SEGURIDAD.md` y `ALERTAS.md`: los crean las skills de seguridad y datos.

Así, en cada chat nuevo Claude ya sabe qué es tu sistema y qué se hizo.

## Instalar

Dentro de Claude Code (terminal o app de escritorio):

```
/plugin marketplace add MateoGaviraghi/dashboard-skills
/plugin install dashboard@dashboard-skills
```

Reiniciá Claude Code si las skills no aparecen al escribir `/dashboard`.

## Actualizar

```
/plugin marketplace update dashboard-skills
```

## Primer mensaje recomendado

Abrí Claude Code en la carpeta de tu sistema y pegá esto:

```
Instalé el plugin "dashboard" (skills /dashboard:diseno, /dashboard:seguridad y /dashboard:datos).

Antes de cambiar nada, quiero que entiendas mi sistema:
1. Leé el repo entero: stack y versiones, rutas/pantallas, cómo se consulta la base, cómo funciona el login y los roles, dónde están los estilos y componentes de UI.
2. Escribí docs/dashboard/SISTEMA.md con el formato que define la skill diseno (Paso 0). Donde no estés seguro, poné {{CONFIRMAR}} y preguntame; no inventes.
3. Creá docs/dashboard/DECISIONES.md vacío con el título.
4. Agregá a mi CLAUDE.md (crealo si no existe) esta línea: "Antes de trabajar, leé docs/dashboard/SISTEMA.md y docs/dashboard/DECISIONES.md. Para diseño usá /dashboard:diseno, para seguridad /dashboard:seguridad, para filtros/búsqueda/paginación/velocidad/alertas /dashboard:datos."
5. Devolveme un diagnóstico corto en tres bloques: Diseño, Seguridad y Datos. En cada uno, los 3 problemas más importantes que ves, con archivo y línea.

No modifiques código todavía. Cuando termines, decime por cuál de los tres te parece que empecemos y por qué.
```

Después, para cada tema:
- **Diseño:** `/dashboard:diseno no me gusta la tabla de pedidos, se ve amontonada`
- **Seguridad:** `/dashboard:seguridad revisá todo el sistema`
- **Datos:** `/dashboard:datos la pantalla de clientes tarda mucho` o `/dashboard:datos quiero alertas de stock bajo`

## Cómo trabajan

- **No cambian nada sin tu aprobación:** primero diagnóstico y plan, después tu "dale", después código.
- **Una cosa por vez.**
- Al terminar te reportan en cuatro partes: **Hecho**, **Falta**, **Sigue** y **De vos**.

## Licencia

MIT
