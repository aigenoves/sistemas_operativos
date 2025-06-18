# Tema 5: cgroups, Namespaces y Contenedores

Este documento explora la virtualización ligera en Linux, centrándose en las características del kernel que permiten el aislamiento de recursos y procesos, culminando en la explicación de los contenedores y herramientas como Docker y Podman.

## 1. [`chroot`][chroot]

* Es una técnica para **aislar aplicaciones** cambiando el **directorio raíz aparente** de un proceso.
* Introducido en UNIX v7 en 1979.
* Afecta al proceso y a sus hijos, **impidiendo el acceso a archivos y comandos fuera** del nuevo directorio raíz.
* El entorno creado se llama "**jail chroot**".
* Permite que los procesos usen sus **propias versiones de bibliotecas** y ejecutar programas de otras distribuciones, pero **compartiendo el mismo kernel**.

## 2. [Control Groups (`cgroups`)][cg]

* Característica del kernel de Linux que permite organizar procesos en **grupos jerárquicos**.
* Su propósito es **limitar y monitorear el uso de recursos** (CPU, memoria, I/O, etc.).
* Desarrollado en Google (2006, como "process containers"), renombrado a "Control Groups" en 2007, disponible desde el kernel 2.6.24.
* Ofrecen:
  * **Resource Limiting**: Evitar que los grupos excedan el uso de un recurso.
  * **Prioritization**: Asignar prioridad a un grupo en el uso de recursos.
  * **Accounting**: Medir el uso de recursos por un grupo.
  * **Control**: Congelar y reiniciar un grupo de procesos.
* La interfaz se expone a través de un **pseudo-filesystem** (generalmente `/sys/fs/cgroup`).
* Cada subsistema (ej. `cpu`, `memory`) representa un **único recurso**.
* Los cgroups se organizan en una **jerarquía** donde los subdirectorios reflejan dependencia; los atributos padres no pueden ser excedidos por los hijos.
* Un proceso puede pertenecer a un cgroup dentro de una jerarquía, pero a **varias jerarquías simultáneamente**.
* Un proceso creado con `fork()` hereda el cgroup de su padre.

### cgroups v1

* Controladores podían montarse en pseudo-filesystems individuales (ej. `mount -t cgroup -o cpu none /sys/fs/cgroup/cpu`) o todos juntos.
* Permitía asignar hilos de un proceso a diferentes cgroups (con limitaciones en v2).

### cgroups v2

* Diseñado como reemplazo de v1.
* Todos los controladores se montan en una **jerarquía unificada** (ej. `mount -t cgroup2 none /sys/fs/cgroup2`).
* **No permite procesos internos** en cgroups que tengan hijos (excepto el raíz); los procesos deben asignarse a **cgroups hoja**.
* Los controladores disponibles en un cgroup son los que el padre habilitó para sus hijos (`cgroup.subtree_control`).

## 3. [Namespaces][ns]

* Permiten **aislar lo que los procesos ven**, haciendo que cada proceso (o conjunto de procesos) opere dentro de su propia instancia aislada de un recurso global.
* Las modificaciones a un recurso quedan contenidas dentro del `namespace`.
* Tipos de Namespaces en Linux:
  * **IPC**: Aísla comunicación entre procesos (System V IPC, colas de mensajes POSIX).
  * **Network**: Aísla dispositivos de red, pilas de protocolos, puertos.
  * **Mount**: Aísla puntos de montaje, permitiendo vistas distintas de la tabla de montajes.
  * **PID**: Aísla IDs de procesos, permitiendo múltiples árboles de procesos anidados. Un proceso tiene un PID en su propio namespace y otro en el host.
  * **User**: Aísla IDs de usuarios y grupos. Un usuario `root` dentro del contenedor puede ser un usuario sin privilegios en el host. No se requiere ser `root` para crear un `user namespace`.
  * **UTS**: Aísla el hostname y el nombre de dominio.
  * **Cgroup**: Aísla el directorio raíz de cgroup.
  * **Time**: Aísla los offsets al reloj del sistema.
* Se asignan procesos a un nuevo namespace al crearlos (`clone()`) o a un proceso en ejecución (`unshare()`, `setns()`).
* Un proceso hijo hereda todos los namespaces de su padre.
* Un namespace se elimina automáticamente cuando el último proceso lo abandona.
* Cada proceso tiene un subdirectorio en `/proc/[pid]/ns` que contiene los namespaces asociados.

## 4. Contenedores (Docker y Podman)

* **Tecnología ligera de virtualización a nivel de sistema operativo** que permite ejecutar múltiples sistemas aislados en un único host.
* **Historia breve**: Desde `chroot` (1979) hasta LXC (2008), **Docker** (2013), **Kubernetes** (2014) y **Podman** (2019).
* Tipos:
  * **De sistemas operativos**: Ejecutan un SO completo (menos el kernel) (ej. LXC).
  * **De aplicaciones**: Empaquetan una aplicación o proceso (ej. Docker, Podman).
* **Características Principales**:
  * **Autocontenidos**: Tienen todo lo necesario para funcionar.
  * **Aislados**: Mínima influencia en el nodo y otros contenedores.
  * **Independientes**: Su administración no afecta al resto.
  * **Portables**: Pueden ejecutar de igual manera en diferentes entornos.
  * Ejecutan en **modo usuario usando un kernel compartido**.
  * Cada contenedor suele proveer un **único servicio** ("micro-servicio").
  * Desde el host, un contenedor es un proceso (o conjunto de procesos).

### Cómo funciona Docker

* Utiliza características del kernel:
  * **Namespaces**: Para el espacio de trabajo aislado (PID, Network, IPC, Mount).
  * **Control Groups**: Para limitar los recursos asignados.
  * **Union File Systems**: Para el sistema de archivos de los contenedores (ej. `overlay2`). Permiten apilar directorios de solo lectura con una capa superior escribible. Cada contenedor tiene su propia capa escribible.

### Definiciones Clave de Docker

* **Imagen**: Un **molde de solo lectura** con instrucciones para construir un contenedor.
* **Contenedor**: Una **instancia en ejecución** de una imagen. La capa escribible se elimina al destruir el contenedor.
* **Registry**: Un almacén de imágenes (ej. Docker Hub).
* **Dockerfile**: Archivo de texto que define los pasos para construir una imagen. Cada instrucción genera una capa en la imagen.

### Almacenamiento Persistente en Docker

* Los datos en la capa escribible no persisten. Para ello, Docker ofrece:
  * **Volumes**: Almacenados en una parte del filesystem administrada por Docker (ej. `/var/lib/docker/volumes`). Más portables.
  * **Bind Mounts**: Pueden estar en cualquier parte del filesystem, modificables por procesos no Docker.

### Docker Compose

* Herramienta para **definir y ejecutar aplicaciones Docker multi-contenedor**.
* Usa un archivo **YAML** (`docker-compose.yml`) para describir los servicios (contenedores) a desplegar.
* Simplifica la orquestación de sistemas complejos.
* Permite definir política de reinicio y dependencias de arranque.
* Crea una **red única por defecto** para la comunicación entre contenedores.

### Podman

* Abreviación de "Pod Manager".
* **Container engine daemonless** para desarrollar, administrar y ejecutar contenedores OCI en Linux.
* Utiliza **prácticamente los mismos comandos que Docker**.
* Concepto de **Pod**: uno o más contenedores que **comparten los mismos namespaces** (PID, networking, IPC) y almacenamiento, con una **única IP**. Se comunican usando `localhost`.
* Cada Pod incluye un **contenedor `infra`** (o `pause container`) para mantener abiertos los namespaces del Pod.
* `conmon` es un programa C ligero que monitorea el proceso principal de un contenedor dentro de Podman.

### Estandarización y Orquestación

* **Open Container Initiative (OCI)**: Iniciada por Docker, RedHat, AWS, Google. Se encarga de la especificación del runtime y las imágenes de contenedores, lanzando `runc` como primera implementación.
* **Containerd**: El runtime usado por Docker Engine.
* **Orquestadores de contenedores**: Herramientas como **Kubernetes**, Docker Swarm, RedHat Openshift, Rancher, que permiten aprovisionar, desplegar, escalar y administrar automáticamente contenedores en un cluster.

[chroot]: https://github.com/aigenoves/sistemas_operativos/blob/main/resumenes/tema_5/chroot.md
[cg]: https://github.com/aigenoves/sistemas_operativos/blob/main/resumenes/tema_5/cgroups.md
[ns]: https://github.com/aigenoves/sistemas_operativos/blob/main/resumenes/tema_5/namespaces.md
