> [!IMPORTANT]
> Este repositorio es una **exhibición de arquitectura** — no contiene código fuente.
> El sistema Vigilia es desarrollado de forma privada por [@Bajmein](https://github.com/Bajmein).
> Lo que encontrarás aquí: documentación técnica del pipeline, plantillas de infraestructura y el diagrama del sistema.

# Vigilia

Sistema de análisis de video en tiempo real con detección de eventos, toma de decisiones autónoma y respuesta configurada. Diseñado para entornos con restricciones de latencia y privacidad.

---

## Pipeline de Datos

```mermaid
flowchart LR
    subgraph Ingestión
        A[Cámaras RTSP] --> B[vigilia-ingest]
        B --> C[MQTT Broker]
    end

    subgraph Análisis GPU
        C --> D[vigilia-analyzer]
        D -->|frames + metadata| E[IPC Socket]
    end

    subgraph Decisión Python
        E --> F[vigilia-orchestrator]
        F -->|eventos clasificados| G{Motor de Reglas}
        G -->|umbral superado| H[Acción]
    end

    subgraph Acción
        H --> I[MQTT Publish]
        H --> J[Audio Disuasorio]
        H --> K[Grabación de Clip]
        H --> L[VMS / Almacenamiento]
    end
```

---

## Stack

| Capa | Tecnología | Rol |
|------|-----------|-----|
| Ingestión | GStreamer + Python | Captura de streams RTSP y decodificación |
| Análisis | CUDA + TensorRT | Inferencia en GPU (detección de objetos/personas) |
| Mensajería | Eclipse Mosquitto | Bus MQTT inter-servicio |
| Orquestación | Python (asyncio) | Motor de reglas y toma de decisiones |
| Infraestructura | Docker Compose | Despliegue multi-servicio con soporte GPU |
| Almacenamiento | Volúmenes Docker | Retención de clips y logs de eventos |
| Acceso remoto | Tailscale + WHEP | Acceso seguro a streams y panel de control |

---

## Inicio Rápido (Infraestructura)

> Requiere Docker Engine con soporte NVIDIA Container Toolkit.

```bash
# Copiar plantillas de configuración
cp docker-compose.example.yml docker-compose.yml
cp config.example.yaml config.yaml

# Editar credenciales y parámetros (ver comentarios en cada archivo)
# ...

# Levantar los servicios
docker compose up -d

# Ver logs del analizador
docker compose logs -f vigilia-analyzer
```

---

## Estado

**Versión:** `v0.8.1`

El sistema se encuentra en fase de producción controlada. Las siguientes capacidades están operativas:

- Pipeline de detección en tiempo real con GPU
- Motor de reglas configurable por zona
- Integración con VMS externos
- Acceso remoto via Tailscale

---

## Licencia

El código fuente de Vigilia es **privado** y no se encuentra disponible en este repositorio.

Este repositorio existe exclusivamente como referencia de arquitectura y como muestra de capacidades técnicas.

Para consultas, contactar a [@Bajmein](https://github.com/Bajmein).
