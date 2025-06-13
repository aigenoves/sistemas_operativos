# Namespaces

Los **Namespaces** son una característica fundamental del kernel de Linux que permite **aislar los recursos globales del sistema para que los procesos dentro de un "namespace" piensen que tienen su propia instancia aislada de ese recurso.** En esencia, limitan lo que un proceso puede ver y, por consiguiente, lo que puede usar. Las modificaciones a un recurso quedan contenidas dentro de su respectivo namespace.

## Propósito y Capacidades

- Aislamiento de Procesos: Los namespaces son un mecanismo de seguridad en Linux que permite aislar procesos.
- Vista Aislada de Recursos: Cada proceso, o conjunto de procesos, puede tener una vista distinta de un recurso global. Por ejemplo, pueden tener su propia tabla de montajes o sus propios IDs de usuario y grupo.
- Coexistencia: Una característica clave es que un proceso solo puede pertenecer a un namespace de un tipo a la vez. Un namespace se elimina automáticamente cuando el último proceso que reside en él finaliza o lo abandona.
- Herencia: Un proceso hijo hereda todos los namespaces de su proceso padre. Un proceso puede utilizar ninguno, algunos o todos los namespaces de su padre.

## Mecanismo de Implementación

La interfaz del kernel para los namespaces se gestiona a través de tres llamadas al sistema (```system-calls```) principales:

- ```clone()```: Similar a ```fork()```, crea un nuevo proceso y lo asigna al nuevo namespace especificado mediante flags.
- ```unshare()```: Agrega el proceso actual a un nuevo namespace, creando el nuevo namespace y haciendo que el proceso llamante sea miembro de él.
- ```setns()```: Permite agregar un proceso en ejecución a un namespace existente, desasociándolo de una instancia de un tipo de namespace y reasociándolo con otra instancia del mismo tipo.

Para verificar los namespaces a los que un proceso está asociado, se puede consultar el subdirectorio ```/proc/[pid]/ns```

## Tipos de Namespaces Provistos por Linux

- **IPC (```CLONE_NEWIPC```):** Aísla los recursos de comunicación entre procesos (System V IPC, colas de mensajes POSIX).
- **Network (```CLONE_NEWNET```):** Aísla los dispositivos de red, pilas de protocolos, puertos, etc.
- **Mount (```CLONE_NEWNS```):** Aísla los puntos de montaje, lo que significa que cada proceso puede tener su propia tabla de montajes.
- **PID (```CLONE_NEWPID```):** Permite tener múltiples árboles de procesos anidados y aislados. Los procesos en un namespace PID pueden tener un PID diferente al que tendrían en el host principal-
- **User (```CLONE_NEWUSER```):** Aísla los IDs de usuarios y grupos. Un usuario con ID 0 (root) dentro de un contenedor puede tener una identidad no root y no privilegiada en el host. No se requiere ser root para crear un namespace de usuario. Los mapeos entre IDs internos y externos se gestionan a través de ``/proc/PID/uid_map`` y ``/proc/PID/gid_map``.
- **UTS (``CLONE_NEWUTS``):** Aísla el hostname y el nombre de dominio.
- **Cgroup (``CLONE_NEWCGROUP``):** Aísla el directorio raíz de cgroup.
- **Time (``CLONE_NEWTIME``):** Permite distintos offsets al reloj del sistema por namespace.

**Namespaces y Contenedores:** Los namespaces son una de las características clave del kernel que, junto con los **Control Groups (cgroups)**, son utilizadas por tecnologías de contenedores como **Docker** y **Podman** para proporcionar un entorno aislado y limitado.

- **Docker** utiliza namespaces para crear el espacio de trabajo aislado de un contenedor. Por cada contenedor, Docker crea un conjunto de namespaces (incluyendo PID, red, IPC y montaje).

- **Podman** extiende este concepto con los "Pods", que son grupos de uno o más contenedores que comparten los mismos namespaces (típicamente PID, red e IPC). Dentro de un Pod, un contenedor especial llamado ``infra`` o ``pause container`` se encarga de mantener abiertos los namespaces compartidos. Esto permite que los procesos dentro del Pod se comuniquen entre sí usando localhost.

En resumen, los namespaces proporcionan una capa de aislamiento crucial para los contenedores, permitiendo que las aplicaciones empaquetadas en ellos se ejecuten de forma autónoma y sin interferir con otros procesos o el sistema operativo host.
