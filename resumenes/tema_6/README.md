# Contenedores - Docker

Este documento presenta una visión general de Docker, una plataforma de virtualización ligera, y cómo utiliza características del kernel de Linux para operar.

## ¿Qué es Docker?

**Docker** es una **plataforma de código abierto** que permite empaquetar y ejecutar aplicaciones en **contenedores ligeros**. Se compone de tres elementos principales:

* **Docker Daemon (`dockerd`)**: El servidor responsable de crear, ejecutar y monitorear contenedores, así como de construir imágenes.
* **API**: Define la interfaz que los programas utilizan para interactuar con el servidor.
* **CLI (Command Line Interface)**: El cliente que permite a los usuarios interactuar con el servidor mediante comandos.

Docker opera con una arquitectura **cliente-servidor**. El cliente y el servidor pueden residir en el mismo sistema (comunicándose a través de IPC o un socket de dominio) o en nodos diferentes (usando un socket TCP/IP). Ambos componentes, cliente y servidor, **se ejecutan en el espacio de usuario** del sistema operativo.

## ¿Cómo funciona Docker?

Docker aprovecha varias características del kernel de Linux para proporcionar el aislamiento y la gestión de recursos de los contenedores:

### Namespaces

Docker utiliza Namespaces para crear el **espacio de trabajo aislado** de un contenedor. Por cada contenedor, Docker genera un conjunto de namespaces, incluyendo:

* **PID Namespace**: Permite que los procesos dentro del contenedor tengan sus propios IDs de proceso, aislados de los IDs de proceso del host. Un proceso en el contenedor puede tener PID 1, mientras que en el host tendrá un PID diferente.
* **Network Namespace**: Aísla los dispositivos de red, pilas de protocolos y puertos, otorgando a cada contenedor su propia configuración de red.
* **IPC Namespace**: Aísla los recursos de comunicación entre procesos (como semáforos o colas de mensajes POSIX).
* **Mount Namespace**: Aísla la tabla de montajes, lo que significa que cada contenedor tiene una vista distinta de los puntos de montaje del sistema de archivos.

### Control Groups (cgroups)

Los **cgroups** son utilizados por Docker para, opcionalmente, **limitar y monitorear el uso de recursos** asignados a un contenedor. Esto incluye recursos como tiempo de CPU, cantidad de CPUs, memoria, y operaciones de I/O.

### Union File Systems

Los sistemas de archivos de unión (como `overlay2`, AUFS, btrfs) son fundamentales para el almacenamiento de los contenedores en Docker. Funcionan apilando varios directorios en un mismo punto de montaje, los cuales aparecen como un único sistema de archivos.

* Las **capas inferiores** de una imagen son de **solo lectura (inmutables)**.
* La **capa superior** es **escribible** y es exclusiva de cada instancia de contenedor. Cuando se elimina un contenedor, esta capa escribible se pierde, mientras que las capas de imagen subyacentes permanecen inalteradas.
* Esta arquitectura permite la **reutilización de capas** entre distintas imágenes y contenedores, optimizando el espacio y la velocidad.

## Definiciones Clave de Docker

* **Imagen**: Es un **molde (template) de solo lectura** que contiene todas las instrucciones necesarias para construir un contenedor. Las imágenes se componen de capas apiladas.
* **Contenedor**: Es una **instancia en ejecución** de una imagen. Cada contenedor es autónomo, aislado y comparte el mismo kernel del sistema operativo base.
* **Registry**: Un almacén de imágenes de Docker. **Docker Hub** es el registro público predeterminado.
* **Dockerfile**: Un archivo de texto que define los pasos e instrucciones para construir una imagen de Docker. Cada instrucción en un Dockerfile suele generar una nueva capa en la imagen resultante.

## Almacenamiento Persistente

Los datos escritos directamente en la capa escribible de un contenedor **no persisten** una vez que el contenedor es eliminado. Para la persistencia de datos, Docker ofrece:

* **Volumes**: Almacenados en una parte del sistema de archivos del host administrada por Docker (generalmente en `/var/lib/docker/volumes`). Son más portables.
* **Bind Mounts**: Pueden ubicarse en cualquier parte del sistema de archivos del host y pueden ser modificados por procesos fuera de Docker.

Ambas opciones deben montarse explícitamente en el contenedor.

## Redes (Networking)

Los contenedores tienen la red habilitada por defecto y pueden realizar conexiones salientes.

* Se pueden **definir redes personalizadas** para que múltiples contenedores se comuniquen entre sí usando sus direcciones IP o nombres.
* Un contenedor puede conectarse a varias redes simultáneamente.
* Para que un servicio dentro de un contenedor sea accesible desde fuera del host o desde otros contenedores en diferentes redes, se debe **publicar el puerto** correspondiente. No es posible que dos contenedores en el mismo host publiquen el mismo puerto.
* Los contenedores utilizan los mismos servidores DNS que el nodo host, aunque esto puede modificarse.

## Ejemplos de Funcionamiento

Cuando se inicia un contenedor, Docker crea nuevos namespaces y lo asocia con cgroups.
Por ejemplo, si se ejecuta un contenedor y se listan los namespaces en el host, se verán nuevos namespaces (PID, IPC, MNT, NET, UTS) asociados al proceso del contenedor, distintos de los namespaces raíz del sistema.
**Ejemplo de `lsns` en el host antes de Docker (muestra namespaces raíz)**:

NS        TYPE   NPROCS   PID USER    COMMAND 4026531835 cgroup     88     1 root    /sbin/init 4026531836 pid        88     1 root    /sbin/init 4026531837 user       88     1 root    /sbin/init 4026531838 uts        88     1 root    /sbin/init 4026531839 ipc        88     1 root    /sbin/init 4026531840 mnt        86     1 root    /sbin/init 4026531992 net        88     1 root    /sbin/init

**Después de iniciar un contenedor (`docker run -d --name contenedor1 busybox /bin/sh -c "sleep 5000"`)**:

NS        TYPE   NPROCS   PID USER    COMMAND 4026531835 cgroup     88     1 root    /sbin/init 4026531836 pid        87     1 root    /sbin/init 4026531837 user       88     1 root    /sbin/init 4026531838 uts        87     1 root    /sbin/init 4026531839 ipc        87     1 root    /sbin/init 4026531840 mnt        85     1 root    /sbin/init 4026531992 net        87     1 root    /sbin/init 4026532236 mnt         1  1722 root    /bin/sh -c sleep 5000 4026532237 uts         1  1722 root    /bin/sh -c sleep 5000 4026532238 ipc         1  1722 root    /bin/sh -c sleep 5000 4026532239 pid         1  1722 root    /bin/sh -c sleep 5000 4026532241 net         1  1722 root    /bin/sh -c sleep 5000

Se observa que el proceso del contenedor (`/bin/sh -c sleep 5000` con PID 1722 en el host) tiene sus **propios IDs de namespace** (ej. `4026532239` para PID), distintos de los del `init` del host.

Al ejecutar `ps -ef` **dentro del contenedor**, el proceso del contenedor se ve con PID 1, demostrando el aislamiento del PID Namespace:

PID USER TIME COMMAND 1 root 0:00 /bin/sh -c sleep 5000 7 root 0:00 ps -ef

Mientras que en el host, el mismo proceso aparece con su PID real:

root 1722 1701 0 21:48 ? 00:00:00 /bin/sh -c sleep 5000

## Comandos Básicos de Docker

Aquí se presentan algunos comandos fundamentales para interactuar con Docker:

* **`docker pull NOMBRE_IMAGEN`**: Descarga una imagen desde un registro (por defecto, Docker Hub).
* **`docker run IMAGEN`**: Ejecuta un contenedor a partir de una imagen.
* **`docker run -d -p HOST_PORT:CONTAINER_PORT --name NOMBRE_CONTENEDOR IMAGEN`**: Ejecuta un contenedor en segundo plano (`-d`), publica un puerto (`-p`), y le asigna un nombre.
* **`docker image build -t NOMBRE:TAG .`**: Construye una imagen a partir de un Dockerfile en el directorio actual.
* **`docker login`**: Inicia sesión en Docker Hub (necesario para subir imágenes).
* **`docker push USUARIO_DOCKERHUB/REPOSITORIO:TAG`**: Sube una imagen a Docker Hub.
* **`docker info`**: Muestra información general y configuración de Docker.
* **`docker ps`**: Lista los contenedores en ejecución.
* **`docker container ls -a`**: Lista todos los contenedores, incluyendo los detenidos.
* **`docker image ls`**: Lista las imágenes disponibles localmente.
* **`docker exec -it CONTENEDOR_ID_O_NOMBRE COMANDO`**: Ejecuta un comando dentro de un contenedor en ejecución de forma interactiva (`-it`).
* **`docker commit CONTENEDOR_ID_O_NOMBRE REPOSITORIO:TAG`**: Crea una nueva imagen a partir de los cambios realizados en un contenedor.
