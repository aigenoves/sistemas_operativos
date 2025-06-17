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

### En qué sentido un archivo compose es diferente a un Dockerfile?

El Dockerfile define CÓMO construir una imagen individual y el archivo Compose define CÓMO desplegar y orquestar múltiples contenedores (servicios) a partir de una o varias imágenes para una aplicación completa.

### Suponga que un usuario desea limitar el uso de CPU de un proceso para que no sea mayor al 80%. ¿Qué mecanismo provisto por el kernel Linux podría utilizar?

El mecanismo provisto por el kernel Linux que un usuario podría utilizar es el de Control Groups (cgroups).
