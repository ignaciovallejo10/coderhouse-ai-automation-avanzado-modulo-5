# Agente RAG para Casa Reposo

Proyecto desarrollado para el Módulo 5 de AI Automation Avanzado de Coderhouse.

El objetivo es implementar un agente conversacional con arquitectura RAG capaz de consultar el Manual Maestro de Conocimiento de Casa Reposo, recuperar información relevante y generar respuestas confiables sin inventar datos que no estén presentes en la documentación.

---

## Objetivo del proyecto

Construir en n8n un sistema de recuperación aumentada por generación que permita:

- Descargar el Manual Maestro desde Google Drive.
- Procesar y fragmentar el documento automáticamente.
- Generar embeddings multilingües.
- almacenar el conocimiento en una base de datos vectorial.
- Recuperar los fragmentos más relevantes ante cada consulta.
- Generar respuestas alineadas con la información del manual.
- Evitar alucinaciones cuando la respuesta no se encuentra en la base de conocimiento.

---

## Arquitectura de la solución

El workflow está organizado en dos ramas independientes:

### 1. Ingesta de conocimiento

```text
Disparador manual
        ↓
Descarga del manual desde Google Drive
        ↓
Carga del documento binario
        ↓
Fragmentación del texto
        ↓
Generación de embeddings con Cohere
        ↓
Almacenamiento de vectores en Pinecone
...
2. Consulta del agente RAG
Consulta del usuario
        ↓
Agente RAG de Casa Reposo
        ↓
Vectorización de la consulta con Cohere
        ↓
Búsqueda semántica en Pinecone
        ↓
Recuperación de los fragmentos relevantes
        ↓
Generación de la respuesta con Gemini

El agente consulta la base vectorial antes de responder y utiliza exclusivamente la información recuperada del Manual Maestro.

Tecnologías utilizadas
Tecnología	Función
n8n	Orquestación de la automatización
Google Drive	Almacenamiento y descarga del documento fuente
Default Data Loader	Lectura del documento binario
Recursive Character Text Splitter	División del manual en fragmentos
Cohere Embed Multilingual v3.0	Generación de embeddings de 1024 dimensiones
Pinecone	Almacenamiento y recuperación vectorial
Google Gemini	Generación de respuestas
GitHub	Documentación y entrega técnica
Configuración técnica
Fragmentación del documento
Chunk Size: 1000
Chunk Overlap: 200
Tipo de entrada: Binary
Detección de formato: automática mediante MIME Type
Método: Recursive Character Text Splitter
Embeddings e índice vectorial
Modelo: Embed-Multilingual-v3.0
Dimensiones: 1024
Índice de Pinecone: casa-reposo-rag
Métrica de similitud: coseno
Infraestructura: serverless
Región: AWS us-east-1

El mismo modelo de embeddings se utiliza durante la ingesta y durante la consulta para garantizar la compatibilidad dimensional.

Recuperación de conocimiento
Operación: Retrieve Documents (As Tool for AI Agent)
Límite de resultados: 4
Metadata: incluida
Reranking: desactivado
Funcionamiento del sistema
El workflow descarga el Manual Maestro desde Google Drive.
El documento se procesa como archivo binario.
El texto se divide en fragmentos con superposición.
Cohere transforma cada fragmento en un vector de 1024 dimensiones.
Pinecone almacena los vectores.
El usuario realiza una consulta desde el chat.
El agente utiliza Cohere para vectorizar la consulta.
Pinecone recupera los cuatro fragmentos más relevantes.
Gemini redacta una respuesta basada exclusivamente en el conocimiento recuperado.
Si el manual no contiene la información solicitada, el agente lo informa sin inventar datos.
Control de alucinaciones

El mensaje de sistema obliga al agente a:

Consultar la base de conocimiento antes de responder.
Utilizar únicamente información respaldada por el Manual Maestro.
No completar datos faltantes con conocimientos externos.
No inventar precios, políticas, características ni procedimientos.
No revelar detalles técnicos internos del sistema.
Mantener un tono profesional, cálido, claro y coherente con Casa Reposo.
Reconocer expresamente cuando la información no está disponible.

Respuesta definida para información inexistente:

No encuentro esa información en el Manual Maestro de Casa Reposo.
Pruebas realizadas
Consulta con información existente

Se consultó al agente sobre los productos de Casa Reposo y sus beneficios principales.

El sistema:

Vectorizó la consulta con Cohere.
Consultó la base vectorial de Pinecone.
Recuperó cuatro fragmentos relevantes.
Generó una respuesta coherente con el Manual Maestro.
Prueba antialucinación

Se preguntó por la dirección física de la sucursal principal de Casa Reposo, dato inexistente en el manual.

El agente respondió:

No encuentro esa información en el Manual Maestro de Casa Reposo.

Esto demuestra que el agente reconoce los límites de la base de conocimiento y evita generar información falsa.

Resultados
Manual procesado correctamente.
17 fragmentos almacenados en Pinecone.
Recuperación semántica validada.
Generación de respuestas basada en contexto.
Control antialucinación validado.
Workflow ejecutado sin errores.
Evidencias

Las capturas del funcionamiento se encuentran en la carpeta evidencias/:

Arquitectura completa del workflow.
Ingesta de 17 fragmentos en Pinecone.
Consulta sobre productos y beneficios.
Prueba de información inexistente.
