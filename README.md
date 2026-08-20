HomeLab Infrastructure: Multi-Instance Minecraft Services and Observability Stack
Este repositorio contiene la configuración de infraestructura como código (IaC) para la gestión, exposición segura y monitoreo de múltiples instancias de servidores de Minecraft y servicios asociados. La solución se ejecuta en un servidor físico local sobre una distribución Arch Linux, utilizando un enfoque basado en contenedores para garantizar el aislamiento y la portabilidad de los servicios.
Arquitectura del Sistema
El sistema está diseñado en tres capas principales que aíslan el tráfico, ejecutan las aplicaciones y monitorizan el rendimiento global.
1. Capa de Red y Exposición (Ingress)

    Exposición de Puertos y Redes: Los contenedores se comunican a través de una red interna bridge (mc-net), exponiendo los puertos de forma controlada hacia el host para el acceso de los clientes de juego.
    Resolución de Nombres: Integración con un cliente No-IP para actualizar dinámicamente la dirección IP pública del servidor físico, vinculándola a un dominio personalizado.

2. Capa de Servicios (Aplicaciones)

    Motores de Servidor: Cinco instancias de servidores de Minecraft ejecutándose en contenedores independientes mediante la imagen optimizada de itzg/minecraft-server.
    Configuración por Entorno: Las instancias se clasifican por versión de juego y tipo de modpack (como Dawncraft o Divine Journey 2), utilizando rutas de almacenamiento persistente montadas desde el host para aislar los archivos de configuración y datos de juego.

3. Capa de Observabilidad

    Recolección de Métricas: Prometheus recopila métricas de rendimiento tanto del sistema operativo del host como de los contenedores en ejecución.
    Visualización: Grafana actúa como la interfaz de visualización, consolidando las métricas en tableros de control para monitorizar el uso de CPU, memoria, almacenamiento y latencia de red en tiempo real.

Implementación de Seguridad (SecOps)
La infraestructura se diseñó bajo principios de seguridad de sistemas para mitigar vectores de ataque comunes en servidores expuestos a internet.
Seguridad a Nivel de Sistema Operativo (Host Hardening)

    Sistema de Base: Instalación minimalista de Arch Linux (LTS) para reducir la superficie de ataque al no incluir paquetes o servicios innecesarios.
    Control de Acceso de Red: Configuración de un cortafuegos (Firewall) con políticas restrictivas por defecto. Solo se permiten puertos esenciales para la comunicación del juego y servicios de administración.
    Control de Acceso Administrativo: El acceso remoto por SSH está restringido mediante el uso obligatorio de llaves criptográficas (deshabilitando el acceso por contraseña) y limitado exclusivamente a una lista blanca de direcciones IP autorizadas.

Seguridad en Contenedores y Datos Sensibles

    Gestión de Secretos: Todas las credenciales, llaves de API de No-IP y contraseñas de bases de datos se manejan mediante variables de entorno locales (archivo .env). Este archivo se encuentra excluido del control de versiones mediante .gitignore para evitar fugas de información en repositorios públicos.
    Aislamiento de Tráfico: Traefik actúa como la única puerta de entrada pública hacia la infraestructura, impidiendo que los puertos de los dashboards internos (como Grafana o la API de Traefik) se expongan directamente a la red externa.

Estructura del Proyecto
El repositorio está organizado de la siguiente manera para facilitar su mantenimiento:

├── .gitignore               # Exclusiones de Git (mundos de MC, archivos .jar, logs y secretos)
├── docker-compose.yml       # Orquestación de servicios (servidores MC, Traefik, Prometheus, Grafana)
├── prometheus.yml           # Configuración de recolección de métricas y objetivos de monitoreo
├── dawncraft/               # NO AGREGADO por archivos de configuración específicos de la instancia Dawncraft
├── divine_journey2/         # NO AGREGADO archivos de configuración específicos de la instancia Divine Journey 2
├── y-0/                     # NO AGREGADO archivos de configuración de instancia adicional 0
├── y-1/                     # NO AGREGADO archivos de configuración de instancia adicional 1

Requisitos de Despliegue
Para replicar esta infraestructura se requiere el siguiente entorno de ejecución:

    Sistema Operativo: Distribución Linux (preferentemente Arch Linux o compatible con Docker).
    Motor de Contenedores: Docker Engine v20.10+ y Docker Compose v2.0+.
    Dependencias de Red: Un dominio o subdominio configurado con el cliente No-IP y puertos redirigidos correctamente en el enrutador local (puertos para Traefik y los servidores de juego).

Instrucciones de Lanzamiento

    Clonar este repositorio en el servidor de destino.
    Crear un archivo .env basado en .env.example y rellenar las variables de entorno con los valores de producción.
    Desplegar el stack completo de servicios utilizando Docker Compose:

docker compose up -d

    Verificar el estado de ejecución de los servicios:

docker compose ps
