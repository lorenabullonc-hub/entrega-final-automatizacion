# Entrega Final – Automatización de Clasificación de Leads VIP

Sistema automatizado de clasificación y gestión de leads mediante Make, Cohere y Notion, con flujo Human-in-the-Loop (HITL) y manejo de errores.

## Documentación principal

La documentación técnica y los entregables del proyecto se encuentran en:

- [Entrega Final Lorena Bullón.pdf](docs/Entrega%20Final%20Lorena%20Bull%C3%B3n.pdf)

## Dashboard de Control

Dashboard público (Notion, Shared View) para visualizar el estado de los leads y la tasa de errores del sistema:

- [Ver Dashboard KPIs](https://app.notion.com/p/3bd94381aa6880968c9ef7797b12a93a?v=3ce94381aa68806c8cc7000cb0276769&source=copy_link)
- [Ver Resumen de Errores](https://app.notion.com/p/3bd94381aa688088bc6fd934c48bdff7?v=3ce94381aa688030b665000ca5c8e2aa&source=copy_link)

## Blueprints de Make

Los archivos contienen los flujos técnicos de los dos escenarios implementados:

- [Escenario 1 – Clasificación y Notificación VIP](blueprints/Escenario%201%20-%20Clasificacion%20y%20Notificacion%20VIP.blueprint.json)
- [Escenario 2 – Contacto Lead Aprobado](blueprints/Escenario%202%20-%20Contacto%20Lead%20Aprobado.blueprint.json)

## Base de datos de Notion

El sistema utiliza una estructura de datos dividida en dos bases relacionadas:

- **Leads**: información de los prospectos, su clasificación (Categoría IA, Justificación), estado del flujo HITL (Pendiente / Procesado por IA / Aprobado por Humano / Contactado) y si son VIP o Estándar.
- **Log de Errores**: errores y eventos registrados cuando un lead llega con datos incompletos, con relación directa al lead correspondiente.

### Acceso a las bases de datos

- Base de datos de Leads: <https://eight-nest-7e0.notion.site/3bd94381aa6880968c9ef7797b12a93a?v=3bd94381aa688083a7fd000cd0812d06&source=copy_link>
- Base de datos de Log de Errores: <https://eight-nest-7e0.notion.site/3bd94381aa688088bc6fd934c48bdff7?v=3ce94381aa688030b665000ca5c8e2aa&source=copy_link>

## Flujo del sistema

**Escenario 1 – Clasificación y Notificación VIP**
1. Trigger de Notion detecta un lead nuevo (Watch Data Source Items).
2. Router valida si el lead tiene datos completos (Mensaje, Empresa, Presupuesto).
   - Si están **incompletos** → se registra el error en la tabla "Log de Errores" sin gastar llamada a la IA.
   - Si están **completos** → continúa al siguiente paso.
3. Cohere clasifica el lead (Categoría: VIP / Estándar + Justificación).
4. Parse JSON estructura la respuesta.
5. Notion se actualiza con la categoría y justificación.
6. Router: si es VIP → Gmail envía correo de aprobación para revisión humana (HITL). Si no es VIP, se marca y continúa el flujo normal.

**Escenario 2 – Contacto Lead Aprobado**
1. Trigger de Notion detecta cambios (updated time) con filtro Estado = "Aprobado por Humano".
2. Gmail envía el correo final de contacto al lead.
3. Notion actualiza el Estado a "Contactado".
