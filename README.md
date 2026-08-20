# HomeLab Infrastructure: Multi-Instance Minecraft Services and Observability Stack

Este repositorio contiene la infraestructura como código (IaC) para la gestión, despliegue y monitoreo de múltiples instancias de servidores de Minecraft y servicios asociados. La solución se ejecuta en un servidor físico local sobre Arch Linux, utilizando contenedores para garantizar el aislamiento y la portabilidad.

## Arquitectura del Sistema

El sistema está diseñado en tres capas principales que aíslan el tráfico, ejecutan las aplicaciones y monitorizan el rendimiento global:

1. **Capa de Red y Exposición**
   - **Exposición de Puertos:** Los contenedores se comunican a través de una red interna bridge (`mc-net`), exponiendo los puertos de forma controlada hacia el host para el acceso de los jugadores.
   - **Resolución de Nombres:** Integración con un cliente No-IP para actualizar dinámicamente la dirección IP pública del servidor físico, vinculándola a un dominio personalizado.

2. **Capa de Servicios (Aplicaciones)**
   - **Motores de Servidor:** Instancias de servidores de Minecraft ejecutándose en contenedores independientes mediante la imagen optimizada de `itzg/minecraft-server`.
   - **Configuración por Entorno:** Las instancias se clasifican por versión y modpack (como Dawncraft o Divine Journey 2), utilizando volúmenes de almacenamiento persistente montados desde el host.

3. **Capa de Observabilidad**
   - **Recolección de Métricas:** Prometheus recopila métricas de rendimiento tanto del sistema operativo del host como de los contenedores en ejecución.
   - **Visualización:** Grafana actúa como la interfaz de visualización, consolidando las métricas en tableros de control para monitorizar el uso de CPU, memoria y almacenamiento en tiempo real.

## Implementación de Seguridad (SecOps)

La infraestructura se diseñó bajo principios de seguridad de sistemas para mitigar vectores de ataque comunes:

- **Host Hardening:** Instalación minimalista de Arch Linux para reducir la superficie de ataque, con un cortafuegos restrictivo que solo permite los puertos esenciales.
- **Acceso Administrativo:** El acceso remoto por SSH está restringido mediante el uso obligatorio de llaves criptográficas y deshabilitando el acceso por contraseña.
- **Gestión de Secretos:** Las credenciales y configuraciones sensibles se manejan mediante variables de entorno excluidas del control de versiones (`.gitignore`).

## Estructura del Proyecto

```text
├── .gitignore          # Exclusiones de Git (mundos de MC, archivos .jar, logs y secretos)
├── docker-compose.yml  # Orquestación de servicios (servidores MC, Prometheus, Grafana, cAdvisor)
├── prometheus.yml      # Configuración de recolección de métricas
├── dawncraft/          # Directorio local de datos y configuración (ignorado en git)
├── divine_journey2/    # Directorio local de datos y configuración (ignorado en git)
├── y-0/                # Directorio local de instancia Bedrock 0 (ignorado en git)
└── y-1/                # Directorio local de instancia Bedrock 1 (ignorado en git)

Requisitos de Despliegue

    Sistema Operativo: Distribución Linux (compatible con Docker).

    Motor de Contenedores: Docker Engine v20.10+ y Docker Compose v2.0+.

Instrucciones de Lanzamiento

    Clonar este repositorio en el servidor de destino.

    Desplegar el stack completo de servicios utilizando Docker Compose:
    Bash

    docker compose up -d

    Verificar el estado de ejecución de los servicios:
    Bash

    docker compose ps
