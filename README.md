# Ritual de Cafés — Checkpoint 1

Pre-entrega 01 de Coderhouse — Automation Avanzado:
«Agente autónomo básico funcional».

Alumno: Mariano Damián Fernandez.

## Workflow

Archivo: `checkpoint1_mariano_fernandez.json`.

Agente de orientación sobre el catálogo de Ritual de Cafés,
con Airtable como herramienta lateral de consulta de solo lectura,
respuesta mediante Chat y notificación final por Gmail.

## Elección del modelo

Se utiliza la API de DeepSeek mediante el nodo nativo
DeepSeek Chat Model de n8n, con el modelo `deepseek-flash`,
por disponibilidad de recursos propios de API.

Esta alternativa fue contemplada expresamente en la respuesta
de Ticher a una consulta previa sobre la sustitución de los
proveedores mencionados en la consigna.

Se mantienen la arquitectura Tools Agent, el System Message
modular, el límite físico de seis iteraciones, la invocación
autónoma de Airtable y el reporte externo de finalización.

## Ejecución

La prueba manual completó el recorrido Airtable → Chat → Gmail
y se recibió la notificación de finalización.

Para ejecutarlo en otra instancia, deben configurarse las
credenciales propias y revisarse el destinatario de Gmail.
