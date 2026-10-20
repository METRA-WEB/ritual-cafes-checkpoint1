# Agente de orientación de catálogo — Ritual de Cafés

Proyecto integrador de **Coderhouse — Automation Avanzado con n8n**. Este repositorio contiene el **Checkpoint 1: Agente autónomo básico funcional**.

Ritual de Cafés es un comercio ficticio multimarca de Adrogué dedicado a cafés y accesorios para su preparación. El proyecto propone un asistente conversacional que ayuda a consumidores domésticos a conocer los productos del catálogo y sus características documentadas.

El agente interpreta las consultas, decide cuándo buscar información en Airtable y responde con los datos recuperados. El workflow finaliza con un correo de supervisión que permite comprobar las herramientas utilizadas y sus resultados.

## Qué hace el agente

- Recibe consultas sobre cafés, presentaciones, molienda, cafeteras y accesorios.
- Decide de forma autónoma cuándo consultar el catálogo de Airtable.
- Busca sobre todos los productos registrados en la tabla configurada, exclusivamente en modo lectura.
- Distingue productos individuales, combos y repuestos para responder según lo solicitado.
- Conserva contexto conversacional breve para interpretar preguntas de seguimiento.
- Comunica la información respaldada y reconoce los datos que no puede confirmar.
- Responde al usuario mediante Chat y envía una notificación de «Tarea completada» por Gmail.

## Arquitectura del flujo

```text
[Chat Trigger] → [AI Agent] → [Chat: respuesta] → [Gmail: Tarea completada]
                    ├─ Chat Model: DeepSeek Chat Model
                    ├─ Memory: Simple Memory
                    └─ Tool: Airtable (Consultar catalogo Ritual Café)
```

| Componente | Configuración |
|---|---|
| Disparador | Chat Trigger |
| AI Agent | Funcionamiento como Tools Agent |
| Chat Model | DeepSeek Chat Model — **deepseek-flash** |
| Guardrail | Max Iterations: **6** |
| System Message | Estructura modular: Rol, Ámbito, Objetivo y Reglas y Escalamiento |
| Memoria | Simple Memory — ventana de contexto **3** |
| Tool | Airtable Search, conectada lateralmente al agente, con descripción contextual de cuándo utilizarla |
| Búsqueda | Término definido por el agente y fórmula construida mediante una expresión con campos fijos |
| Resultados | Return All desactivado; hasta **6 coincidencias por llamada** |
| Respuesta | Nodo Chat con `{{ $json.output }}` |
| Observabilidad | Gmail final y Return Intermediate Steps activado en el agente |

La consulta de Airtable depende de la decisión del agente. La entrega de la respuesta y el envío del reporte constituyen la secuencia determinista posterior.

## Nota sobre el modelo de lenguaje

Se utiliza la API de **DeepSeek** mediante el nodo nativo **DeepSeek Chat Model**, con el modelo **`deepseek-flash`**, por disponibilidad de recursos propios de API.

La consigna menciona OpenAI Chat Model o Anthropic Chat Model y expresa preferencia por GPT-4o o Claude 3.5 Sonnet. Se realizó una consulta previa a Ticher sobre la sustitución del proveedor; su respuesta contempló expresamente el uso de DeepSeek Chat Model como alternativa válida.

La implementación mantiene la arquitectura Tools Agent, el límite físico de iteraciones, la selección autónoma de herramientas y la notificación externa de finalización.

## Guardrails y límites del agente

- **Límite de iteraciones:** máximo de seis vueltas configurado físicamente en el nodo.
- **Herramienta acotada:** Airtable permite consultar el catálogo; el agente no crea, modifica ni elimina registros.
- **Alcance temático:** responde sobre productos y preparación documentada. Ante consultas ajenas, comunica brevemente su ámbito de atención.
- **Información respaldada:** no inventa características, compatibilidades, presentaciones, precios ni existencias.
- **Disponibilidad comercial:** una ficha de catálogo, una estimación o un dato del proveedor no equivalen a stock o precio de venta confirmado de Ritual.
- **Resultados parciales:** el límite de seis coincidencias no restringe la búsqueda a seis productos fijos ni permite afirmar que se consultó exhaustivamente todo el catálogo.
- **Acciones comerciales:** no toma pedidos, reserva unidades, cobra, aplica descuentos ni gestiona pagos o envíos.
- **Información insuficiente:** indica el dato que no puede confirmar o solicita una precisión, sin afirmar derivaciones que no realizó.
- **Privacidad del razonamiento:** el reporte externo incluye herramientas y resultados observables, sin transmitir razonamiento interno.

## Catálogo en Airtable

La base reúne información de productos, categorías, marcas, descripciones, variantes y opciones, estimaciones comerciales, fuentes y observaciones.

La búsqueda textual se realiza sobre estos campos:

`Producto` · `Categoría o colección` · `Marca` · `Descripción breve` · `Variantes y opciones`

El agente aporta un término de búsqueda; la expresión del nodo construye la fórmula. Una consulta general utiliza `TRUE()`, sin restricciones por referencias CP1.

Las observaciones y la procedencia de los datos permiten interpretar las fichas sin confundir información del proveedor con condiciones comerciales confirmadas de Ritual.

## Prueba de funcionamiento

| Consulta realizada | Comportamiento observado | Resultado |
|---|---|---|
| Búsqueda amplia de cafés de Brasil y sus presentaciones | El agente consultó Airtable y recuperó cuatro registros: una referencia individual y tres combos o cajas | Diferenció las categorías, informó presentaciones documentadas y reconoció que no podía confirmar el origen de la ficha individual |

En la ejecución manual se completó el recorrido **Airtable → Chat → Gmail** y se recibió la notificación de finalización.

El correo incluye el identificador de ejecución, el nombre de las herramientas utilizadas y sus resultados observables. La prueba acredita ese recorrido; no demuestra exhaustividad del catálogo.

## Cómo usar el flujo

1. Importá `checkpoint1_mariano_fernandez.json` en n8n mediante **Import from File**.
2. Configurá tus propias credenciales de DeepSeek, Airtable y Gmail.
3. Revisá la base, la tabla y los campos utilizados por la herramienta de Airtable.
4. Configurá tu dirección de destino en Gmail: el export conserva un destinatario fijo.
5. Abrí el chat del workflow y enviá una consulta sobre el catálogo.
6. Comprobá la respuesta, la invocación de Airtable cuando corresponda y la recepción del correo.

## Autoría

**Mariano Damián Fernandez**  
Coderhouse — Automation Avanzado
