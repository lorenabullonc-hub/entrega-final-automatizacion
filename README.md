# Entrega Final – Automatización de Clasificación de Leads VIP

Sistema automatizado de clasificación y gestión de leads mediante Make, Cohere y Notion, con flujo Human-in-the-Loop (HITL) y manejo de errores.

## Estructura del repositorio

```
Entrega-final-Automatizacion/
│
├── README.md
│
├── docs/
│   ├── Entrega Final [Tu Nombre].pdf        # Documento principal de entrega
│   ├── Diagrama de Arquitectura.pdf         # Mapa de ambos escenarios + leyenda HITL/errores
│   ├── Manual Operativo de Datos.pdf        # Esquema de tablas + estructuras JSON de transferencia
│   ├── Matriz de Optimizacion de Costos.pdf # Comparativa GPT-4o mini / Claude Haiku 4.5 / Cohere
│   └── Seguridad y Resiliencia.pdf          # Minimización de datos, rutas de error, prevención de bucles
│
├── blueprints/
│   ├── Escenario 1 - Clasificacion y Notificacion VIP.blueprint.json
│   └── Escenario 2 - Contacto Lead Aprobado.blueprint.json
│
├── evidencia/
│   ├── prueba-1-lead-vip-valido/
│   ├── prueba-2-lead-estandar-valido/
│   ├── prueba-3-lead-datos-incompletos/
│   ├── prueba-4-hitl-end-to-end/
│   └── prueba-5-entrada-malformada/
│
└── video/
    └── enlace-video-demo.txt                # Link a Google Drive/YouTube (no subir el archivo pesado)
```

> Nota: los PDFs y capturas pesadas normalmente no se suben directo a GitHub sin Git LFS; si tu profesor solo necesita verlos, puedes dejarlos en Google Drive/Notion y enlazarlos igual que en el ejemplo, o subir los PDFs livianos directamente al repo.

---

# README.md (contenido sugerido)

## Documentación principal

La documentación técnica y los entregables del proyecto se encuentran en:

- [Entrega Final [Tu Nombre].pdf](docs/Entrega%20Final%20%5BTu%20Nombre%5D.pdf)
- [Diagrama de Arquitectura](docs/Diagrama%20de%20Arquitectura.pdf)
- [Manual Operativo de Datos](docs/Manual%20Operativo%20de%20Datos.pdf)
- [Matriz de Optimización de Costos](docs/Matriz%20de%20Optimizacion%20de%20Costos.pdf)
- [Seguridad y Resiliencia](docs/Seguridad%20y%20Resiliencia.pdf)

## Dashboard de Control

Dashboard público (Notion, Shared View) para visualizar el estado de los leads y la tasa de errores del sistema:

- [Ver Dashboard KPIs](ENLACE_A_TU_VISTA_DASHBOARD_KPIS)
- [Ver Resumen de Errores](ENLACE_A_TU_VISTA_RESUMEN_DE_ERRORES)

## Blueprints de Make

Los archivos contienen los flujos técnicos de los dos escenarios implementados:

- [Escenario 1 – Clasificación y Notificación VIP](blueprints/Escenario%201%20-%20Clasificacion%20y%20Notificacion%20VIP.blueprint.json)
- [Escenario 2 – Contacto Lead Aprobado](blueprints/Escenario%202%20-%20Contacto%20Lead%20Aprobado.blueprint.json)

## Base de datos de Notion

El sistema utiliza una estructura de datos dividida en dos bases relacionadas:

- **Leads**: información de los prospectos, su clasificación (Categoría IA, Justificación), estado del flujo HITL (Pendiente / Procesado por IA / Aprobado por Humano / Contactado) y si son VIP o Estándar.
- **Log de Errores**: errores y eventos registrados cuando un lead llega con datos incompletos, con relación directa al lead correspondiente.

### Acceso a las bases de datos

- Base de datos de Leads: ENLACE_A_TU_BASE_LEADS
- Base de datos de Log de Errores: ENLACE_A_TU_BASE_LOG_DE_ERRORES

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

## Pruebas de estrés realizadas

| # | Prueba | Objetivo |
|---|--------|----------|
| 1 | Lead VIP válido | Confirma clasificación correcta y disparo de correo de aprobación HITL |
| 2 | Lead Estándar válido | Confirma que la rama "No es VIP" no dispara aprobación humana |
| 3 | Lead con datos incompletos | Confirma registro en Log de Errores sin llamar a la IA |
| 4 | Aprobación humana (HITL) end-to-end | Confirma el ciclo completo hasta "Contactado" |
| 5 | Entrada malformada / valor inesperado | Confirma que el sistema no se rompe ante datos con formato inválido |

Evidencia (capturas) de cada prueba disponible en [`/evidencia`](evidencia/).

## Optimización de costos

Comparativa de modelos de IA evaluados (GPT-4o mini, Claude Haiku 4.5, Cohere) y justificación del modelo elegido por tarea — ver [Matriz de Optimización de Costos](docs/Matriz%20de%20Optimizacion%20de%20Costos.pdf).

## Seguridad y resiliencia

Minimización de datos, rutas de error, HITL y prevención de bucles infinitos — ver [Seguridad y Resiliencia](docs/Seguridad%20y%20Resiliencia.pdf).

## Video de demostración

Video demostrativo del funcionamiento del sistema de clasificación de leads VIP, desarrollado con Make, Cohere y Notion. Muestra ambos escenarios: clasificación, notificación VIP, aprobación humana y registro de errores.

[Ver video de demostración](ENLACE_A_TU_VIDEO)
