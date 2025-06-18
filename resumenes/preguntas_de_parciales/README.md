# Resumen parciales años anteriores

Aca hay algunas preguntas realziadas en parciales de años anteriores que pueden estar en este parcial de 2025. Estan separadas por temas

## Kernel

### ¿Qué es el kernel de GNU/Linux? ¿Cuáles son sus funciones principales deltro del Sistema Operativo?

El **kernel de GNU/Linux** es el programa central que actúa como el **núcleo (kernel)** del sistema operativo. Es el software que se encarga de que los programas y el hardware puedan trabajar juntos, y en un sentido estricto, **es el propio Sistema Operativo**.

- **Funciones Principales dentro del Sistema Operativo:**
  - Administración de Recursos
  - Manejo de Memoria
  - Administración de la CPU
  - Administración de Procesos
  - Gestión de E/S (Entrada/Salida)
  - Comunicación y Concurrencia
  - Interacción con los Procesos de Usuario
  - Control de Recursos y Aislamiento

### Explique brevemente qué es un módulo del kernel

Un **módulo del kernel** es un **fragmento o pedazo de código** que puede **cargarse y descargarse dinámicamente en el mapa de memoria del sistema operativo (el kernel) bajo demanda**. Su función principal es **extender la funcionalidad del kernel "en caliente"**, es decir, sin necesidad de recompilar todo el kernel o reiniciar el sistema.

### Describa los motivos que puede tener un usuario de Linux para compilar un kernel

Un usuario de Linux puede compilar un kernel por varios motivos, principalmente por:

- **Personalización**: Permite adaptar el kernel a su hardware específico, incluyendo solo los drivers y módulos necesarios o habilitando funcionalidades no comunes como el soporte para sistemas de archivos BTRFS o dispositivos de Loopback.
- **Optimización**: Para reducir el tamaño del kernel, optimizar el uso de memoria o mejorar el tiempo de arranque, decidiendo si las funcionalidades se integran directamente ("built-in") o se cargan dinámicamente como módulos.
- **Desarrollo y Experimentación**: Ayuda a comprender en profundidad el kernel y sus conceptos, y es esencial para implementar y probar nuevas System Calls o desarrollar y depurar nuevos módulos y drivers.
- **Control**: Ofrece control total sobre el código binario que se ejecutará, permitiendo incluso verificar la firma criptográfica del código fuente para mayor seguridad.

### ¿Qué ventaja/s provee compliar una funcionalidad como módulo respecto a compilarla como built-in?

Compilar una funcionalidad como módulo (en lugar de "built-in") provee las siguientes ventajas:

- Carga y descarga dinámica
- No requiere reiniciar el sistema
- Kernel más pequeño y menor uso de memoria
- Mayor modularidad
- Facilidad de actualización y modificación
- No es necesaria la recompilación completa del kernel

### ¿Qué ventaja/s provee compliar una funcionalidad como built-in respecto a compilarla como módulo?

Compilar una funcionalidad como "built-in" (integrada directamente en el kernel) respecto a compilarla como módulo provee la siguiente ventaja principal:

- Mayor eficiencia en su utilización
- Acceso directo a la funcionalidad

### ¿Que contiene el initramfs? ¿Qué funcionalidad/es provee?

El `initramfs` (initial RAM filesystem) es un sistema de archivos temporal que se monta durante el arranque del sistema Linux. Asegura que el kernel pueda acceder al sistema de archivos raíz y completar el arranque.

**Contenido principal:**

- Módulos del kernel necesarios para acceder al sistema de archivos raíz (`/`).
- Herramientas básicas (`mount`, `ls`, `chroot`, `udev`).
- Drivers para hardware crítico (discos, controladores RAID/LVM, sistemas de archivos).
- Scripts de inicialización (`/init`) que preparan el entorno para el arranque real.

**Funcionalidades clave:**

- `Montar el sistema de archivos raíz` (cuando está en particiones cifradas, remotas, LVM, etc.).
- `Cargar módulos del kernel` necesarios para hardware específico.
- **Proporcionar un entorno mínimo** para resolver dependencias antes de pasar el control al sistema principal (`/sbin/init`).
- **Soporte para arranques especiales** (rescate, recuperación, o sistemas con configuración compleja).

### ¿Qué es un target "phony"?

Un target "phony" en un Makefile es una regla que no representa un archivo físico en el sistema, solo define una acción a ejecutar (como compliar, limpar, ejecutar test, etc.)

## System Calls

### ¿Qué es libc?

Es la Interfaz de Programación de Aplicaciones (API) principal del sistema operativo en entornos UNIX y GNU/Linux. Es una interfaz entre las aplicaciones de usuario y las System Calls (System Call Wrappers)

### ¿Las system calls en que modo se ejecutan?

Las system calls (llamadas al sistema) se ejecutan en modo privilegiado (modo Kernel)

### ¿Cómo se identifican a las system calls?

Las system calls se identifican a través de un número único (conocido como syscall number)

### ¿Cada drivers implementa una System Call?

Los drivers no implementan una nueva `System Call` por cada uno de ellos o por cada operación que realizan. En cambio, los drivers proveen la lógica subyacente y específica del dispositivo para que el kernel pueda manejar y ejecutar las operaciones solicitadas a través de `System Calls` genéricas como `read`, `write`, `open`, `close` y `ioctl`.

### ¿Cómo se gestionan las system calls en Linux?

*COMPLETAR*

## Docker

### ¿Qué es una imagen?

- Un **`template` (molde) de solo lectura** con todas las instrucciones necesarias para construir un contenedor
- Es el **punto de partida** para crear una o varias instancias de contenedores

### ¿Qué es un contenedor?

- Una **instancia en ejecución de una imagen**
- Una **tecnología de virtualización liviana**
- Desde la perspectiva del host, un contenedor es un **proceso** (o un conjunto de procesos) ejecutándose

### ¿Cual es la diferencia entre una imagen y un contenedor?

 La imagen es el "plano" o "molde" inmutable, y el contenedor es la "realización" o "instancia en ejecución" de ese plano, con una capa escribible para su funcionamiento dinámico

### ¿Qué caracteristicas del kernel de Linux utiliza docker para proveer containers?

- **Namespaces**: Para aislar el espacio de trabajo de cada contenedor, incluyendo IDs de proceso (pid), red (net), comunicación entre procesos (ipc) y puntos de montaje (mnt).

- **Control Groups (cgroups)**: Para limitar y monitorear el uso de recursos asignados a un contenedor, como CPU, memoria e I/O.

- **Union file systems**: Como sistema de archivos del contenedor, permitiendo apilar capas inmutables de la imagen y agregar una capa superior de lectura/escritura para los datos del contenedor.

- **Kernel compartido**: Todos los contenedores comparten el mismo kernel del sistema operativo base (host).

### ¿Qué es un Dockerfile?

Un archivo de texto que contiene las instrucciones necesarias para construir una imagen

### ¿Qué es un archivo compose?

Un archivo de texto escrito en lenguaje `YAML` que contiene las instrucciones y la configuración para desplegar aplicaciones compuestas por múltiples contenedores.

### ¿En qué sentido un archivo compose es diferente a un Dockerfile?

El Dockerfile define CÓMO construir una imagen individual y el archivo Compose define CÓMO desplegar y orquestar múltiples contenedores (servicios) a partir de una o varias imágenes para una aplicación completa.

### Suponga que un usuario desea limitar el uso de CPU de un proceso para que no sea mayor al 80%. ¿Qué mecanismo provisto por el kernel Linux podría utilizar?

El mecanismo provisto por el kernel Linux que un usuario podría utilizar es el de Control Groups (cgroups).

### ¿Qué es Union Filesystem? ¿Cómo lo utiliza docker?

 **Union Filesystem** es un sistema de archivos que combina múltiples directorios (o "capas") en una sola vista unificada, sin modificar los archivos originales. Las operaciones de escritura se manejan en una capa superior, preservando las capas base como solo lectura.

 **Características:**

- Copy-on-Write (CoW): Los cambios se escriben en una nueva capa, no en los archivos originales.

- Superposición de capas: Permite apilar sistemas de archivos (ej: una imagen base + modificaciones).

- Eficiencia: Reduce duplicación de datos y acelera la creación de contenedores.

**Docker** utiliza UnionFS (a través de OverlayFS, AUFS, o devicemapper) para:

- **Imágenes multicapa**:

  - Cada instrucción en un Dockerfile crea una nueva capa (ej: RUN, COPY).

  - Las capas son inmutables y compartidas entre contenedores, optimizando almacenamiento.

- **Contenedores efímeros**:

  - Al iniciar un contenedor, Docker añade una capa de escritura temporal sobre la imagen base.

  - Los cambios se guardan aquí y se pierden al eliminar el contenedor (a menos que se persistan con volúmenes).

### ¿De que manera puede lograrse que los datos sean persistentes en docker? ¿Qué dos maneras hay de hacerlo? ¿Cuáles son las diferencias entre ellas?

En Docker, los datos se pueden hacer persistentes de dos maneras principales: a través de **Volumes (volúmenes)** y **Bind Mounts (montajes de enlace)**.

#### Diferencias entre Volúmenes y Bind Mounts

| Característica               | Volúmenes Docker                          | Bind Mounts                          |
|------------------------------|------------------------------------------|--------------------------------------|
| **Ubicación física**         | En `/var/lib/docker/volumes/`            | En cualquier ruta del host           |
| **Gestión**                  | Administrado por Docker                  | Controlado manualmente por el usuario|
| **Persistencia**             | Sobrevive a contenedores eliminados      | Depende del sistema de archivos host |
| **Portabilidad**             | Alta (fácil migración entre hosts)       | Baja (rutas absolutas específicas)   |
| **Rendimiento**              | Optimizado para Docker                   | Depende del FS del host              |
| **Seguridad**                | Aislado del host                         | Puede acceder/modificar archivos host|
| **Caso de uso principal**    | Datos de aplicaciones en producción      | Desarrollo y configuración           |
| **Creación**                 | `docker volume create`                   | Montaje directo con `-v`             |
| **Backup**                   | Fácil (`docker volume inspect`)          | Requiere herramientas externas       |
| **Compatibilidad multiplataforma** | Sí (comportamiento consistente)   | Limitada (rutas específicas por OS)  |
| **Ejemplo de uso**           | `-v db_data:/var/lib/mysql`              | `-v ./config:/app/config`            |

- 🔄 **Volúmenes** son ideales para bases de datos y datos persistentes
- 💻 **Bind Mounts** son mejores para desarrollo (código fuente, configuraciones)
- 🛡️ Los volúmenes ofrecen mejor aislamiento y seguridad
- 🚀 Los bind mounts permiten edición directa desde el host
