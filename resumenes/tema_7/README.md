# Contenedores - Docker Compose, Podman, y Estandarización

Este resumen cubre Docker Compose, Podman y el concepto de estandarización en el ecosistema de contenedores.

## Docker Compose

**Docker Compose** es una **herramienta** diseñada para **ejecutar aplicaciones que requieren múltiples contenedores**. Cada contenedor en Docker Compose se denomina **"service"** (servicio). Originalmente desarrollado como "Fig" por Orchad y adquirido por Docker en 2014, evolucionó de una versión 1 escrita en Python a una versión 2 escrita en Go en 2020.

Su propósito principal es **facilitar el despliegue** de aplicaciones compuestas por múltiples contenedores que se comunican entre sí para proporcionar una funcionalidad conjunta.

### Archivo Compose

La configuración de Docker Compose se define en un **archivo YAML** (lenguaje de marcado). Por convención, este archivo se llama `compose.yaml` y se busca por defecto en el directorio de trabajo donde se invoca el binario `docker compose`. Este archivo define las características de los contenedores a desplegar.

Existen tres versiones para el formato del archivo `docker compose.yml`: la versión 1 (obsoleta), 2.x y la **3.x (la última y recomendada)**, siendo la 3.9 la más utilizada desde junio de 2020.

### Ventajas y Comandos Básicos

Docker Compose ofrece varias ventajas:

* **Simplicidad**: Toda la configuración está definida en un único archivo con una estructura clara y documentada.
* **Replicabilidad**: Permite desplegar los servicios fácilmente ejecutando el mismo archivo.
* **Compartibilidad ("Shareability")**: Facilita compartir la configuración, solo se necesita proporcionar el archivo compose (asumiendo que las imágenes Docker son accesibles).
* **Versionable**: Su formato de archivo permite usar sistemas de control de versiones como Git.

Comandos fundamentales de Docker Compose:

* `docker compose -v`: Verifica la versión instalada.
* `docker compose build`: Construye imágenes de servicios a partir de Dockerfiles.
* `docker compose create`: Crea todos los contenedores definidos.
* `docker compose up`: Inicia todos los contenedores.
* `docker compose up -d`: Inicia contenedores en segundo plano.
* `docker compose down`: Detiene todos los contenedores.
* `docker compose ps`: Lista los contenedores iniciados.
* `docker compose rm`: Elimina todos los contenedores.
* `docker compose logs`: Muestra los logs de los contenedores.

Otras funcionalidades incluyen:

* Se pueden indicar **políticas de reinicio** para los contenedores.
* Permite definir las **dependencias de arranque** entre contenedores.
* Por defecto, Compose establece una **red `default`** a la que todos los contenedores se unen, y es posible definir **redes propias**.

## Podman

**Podman** es una abreviación de **Pod Manager**. Es un **motor de contenedores "daemonless"** (sin demonio central) para desarrollar, administrar y ejecutar **contenedores OCI** (Open Container Initiative) en sistemas Linux. Utiliza comandos prácticamente idénticos a Docker y soporta imágenes en formato OCI y Docker (v1 y v2). También puede ejecutarse en Windows y MAC.

### Características de Podman

* **Sin demonio central**: Los contenedores inician como procesos estándar del sistema, eliminando la necesidad de un proceso demonio central (como Docker Daemon).
* **Basado en `libpod`**: Utiliza la librería `libpod`, que contiene la lógica para el ciclo de vida de los contenedores (formato de imágenes, autenticación, descarga, almacenamiento, construcción, creación, ejecución, eliminación).
* **Aislamiento**: Proporciona aislamiento de contenedores y pods mediante cgroups a bajo nivel.
* **Soporte rootless**: Permite ejecutar contenedores y pods sin privilegios de root.

### El Concepto de Pods en Podman

El concepto de **Pod** en Podman proviene de Kubernetes. Un **Pod** comprende **uno o más contenedores que comparten los mismos namespaces** (como PID, networking e IPC) y se consideran que trabajan en conjunto con un propósito común. Representa un conjunto de contenedores que comparten **almacenamiento y una única IP**. Los servicios dentro de los contenedores de un pod pueden comunicarse entre sí usando `localhost`.

Ventajas de agrupar contenedores en un Pod:

* Compartir namespaces y cgroups.
* Compartir volúmenes para datos persistentes.
* Compartir la misma configuración.
* Compartir el mismo IPC.

Cada pod incluye un contenedor especial llamado **`infra`** (o "pause container"), cuya finalidad es mantener abiertos los namespaces asociados con el pod. Los procesos de los contenedores agregados al pod comparten varios namespaces del pod, lo que permite la comunicación a través de `localhost` (127.0.0.1).

Para cada contenedor dentro de un pod, existe un proceso `conmon`. **`conmon`** es un programa C ligero que monitorea el contenedor hasta que finaliza, actuando como una herramienta de comunicación entre el motor de contenedores (Podman) y el **runtime OCI** (como `runc` o `crun`). Su tarea principal es monitorear el proceso principal del contenedor, guardar el código de salida y mantener la tty del contenedor abierta.

## Estandarización de Contenedores

La evolución de Docker llevó a la creación de iniciativas de estandarización en la industria.

* Docker inicialmente usó **LXC** como motor de contenedores, pero luego introdujo su propia librería, **`libcontainer`**.
* La compañía CoreOS lanzó **`rkt`**, un motor de contenedores "daemon-less".
* En 2017, la Cloud Native Computing Foundation (CNCF) adoptó `rkt` y **`containerd`** (donado por Docker), siendo `containerd` el runtime usado por Docker Engine junto con `runc`.
* En 2015, Docker, RedHat, AWS, Google y otros iniciaron la **Open Container Initiative (OCI)** bajo el auspicio de la Linux Foundation. OCI se encarga de definir las **especificaciones de runtime y de imágenes** para contenedores. Lanzó la primera implementación de un runtime que cumple con esta especificación: **`runc`**.
* La comunidad de Kubernetes liberó **CRI (Container Runtime Interface)**, un plugin que permite la adopción de una amplia variedad de runtimes. En 2017, RedHat liberó **CRI-O**, que permite el uso de runtimes compatibles con OCI.

### Orquestadores de Contenedores

Un **cluster** es un grupo de nodos interconectados que trabajan juntos. Los **orquestadores de contenedores** son herramientas que permiten aprovisionar, desplegar, escalar y administrar automáticamente contenedores en un cluster, sin preocuparse por la infraestructura subyacente. Ejemplos de orquestadores incluyen **Kubernetes**, Docker Swarm, RedHat Openshift y Rancher.
