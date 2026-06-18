# Clasificador de Incidentes por Email con IA

Workflow de n8n que automatiza la gestión de incidentes técnicos recibidos por Gmail. Clasifica cada incidente con un LLM local (Ollama), analiza su nivel de impacto y notifica por Telegram — todo sin intervención manual.

## Flujo

```
Gmail Trigger → LLM Clasificador → [POSITIVO / NEGATIVO]
                                          ↓
                              LLM Analizador de Impacto
                              (BAJO / MEDIO / ALTO)
                                          ↓
                              Telegram + Google Sheets
```

1. **Gmail Trigger** — detecta emails nuevos con el asunto `INCIDENTE ITU`
2. **Clasificador LLM** — determina si el incidente es `POSITIVO` o `NEGATIVO`
3. **Analizador de impacto** — si es negativo, evalúa nivel (`BAJO`, `MEDIO`, `ALTO`) y genera un resumen y acción sugerida
4. **Notificación Telegram** — envía alerta inmediata para incidentes negativos
5. **Google Sheets** — registra todos los resultados (incluyendo errores)
6. **Gmail** — marca el email como leído para evitar reprocesamiento

## Stack

- [n8n](https://n8n.io/) — orquestador del workflow
- [Ollama](https://ollama.com/) + `llama3.2:3b` — LLM local para clasificación y análisis
- Gmail OAuth2, Google Sheets OAuth2, Telegram Bot

## Requisitos

- n8n instalado (local o en servidor)
- Ollama corriendo con el modelo descargado:

```bash
ollama pull llama3.2:3b
```

- Credenciales configuradas en n8n:
  - Gmail OAuth2
  - Google Sheets OAuth2
  - Telegram Bot
  - Ollama API

## Instalación

1. Abrí n8n e importá el archivo `workflow.json` desde **Import Workflow**
2. Configurá las credenciales en cada nodo
3. Verificá que Ollama esté activo
4. Activá el workflow con el botón **Active**

## Cómo probar

1. Enviá un email al Gmail configurado con el asunto `INCIDENTE ITU`
2. Dejalo como **No leído**
3. El workflow se ejecuta automáticamente (polling cada minuto)

## Resultado esperado

| Clasificación | Acción |
|---|---|
| POSITIVO | Registra en Google Sheets + marca email como leído |
| NEGATIVO | Analiza impacto → notifica por Telegram → registra en Sheets + marca leído |

## Manejo de errores

Si un LLM devuelve un formato inválido o falla, el error se registra en una hoja separada de Google Sheets y el flujo continúa sin detenerse.

## Buenas prácticas aplicadas

- Salida estructurada en JSON para comunicación entre nodos LLM
- Validación con nodos Code (parsers)
- Separación de responsabilidades entre clasificación e impacto
- Manejo de errores persistente
- Sin credenciales en el workflow exportado

## Evidencia

Screenshots del workflow funcionando en `/evidencia`.
