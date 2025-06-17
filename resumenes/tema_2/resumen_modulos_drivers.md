# Resumen: Módulos y Drivers en GNU/Linux

## Módulos del Kernel

Los **módulos del kernel** son "pedazos de código" que pueden ser **cargados y descargados bajo demanda** del sistema operativo. Permiten **extender la funcionalidad del kernel en "caliente"** (sin necesidad de reiniciar el sistema), lo que contribuye a un **diseño más modular** del kernel de Linux. Todo el código de un módulo se ejecuta en **modo Kernel (privilegiado)**, por lo que **cualquier error en un módulo puede provocar un fallo de todo el sistema (Kernel Panic)**.

### Ventajas y Desventajas (Módulos vs. Built-in)

El soporte a una funcionalidad o dispositivo puede integrarse directamente en la imagen del kernel (**built-in**) o como un módulo:

* **Si es built-in**: El kernel **crece en tamaño**, lo que puede llevar a **mayor uso de memoria** y potencialmente **incrementar el tiempo de arranque**. No obstante, su utilización es **más eficiente** ya que no se requiere cargar componentes adicionales en memoria y el acceso es directo.
* **Si es un módulo**: Permite **cargar la funcionalidad solo cuando es necesaria** (**bajo demanda**), lo que resulta en un **menor uso de memoria**. Facilita la **modificación y actualización de drivers** o funcionalidades sin necesidad de recompilar y reiniciar el kernel completo.

El kernel de Linux tiene una tasa de errores en drivers considerablemente más alta que en el resto de su código.

### Gestión de Módulos

Para interactuar con los módulos del kernel, se utilizan comandos específicos:

* `lsmod`: **Lista los módulos actualmente cargados**.
* `rmmod <módulo>`: **Descarga** uno o más módulos.
* `modinfo <módulo>`: Muestra **información detallada** sobre un módulo.
* `insmod <módulo> [opciones]`: Intenta **cargar el módulo** especificado.
* `depmod`: Calcula las **dependencias de los módulos**, esencial para que `modprobe` funcione correctamente.
* `modprobe <módulo> [opciones]`: Carga el módulo y sus **dependencias** automáticamente, o lo descarga.

### Implementación de Módulos

Para escribir un módulo, se deben proveer principalmente dos funciones:

* Una función de **inicialización** (ej., `init_module`), que se ejecuta al cargar el módulo.
* Una función de **descarga** (ej., `cleanup_module`), que se ejecuta al descargar el módulo.

Dentro de los módulos, se utiliza **`printk`** para mensajes de depuración (en lugar de `printf`). La compilación de un módulo se realiza típicamente con un `Makefile` específico y el comando `make modules`, y luego se instala en el directorio `/lib/modules/version-del-kernel`.

## Drivers

Un **driver** es el código específico que lleva a cabo cada operación sobre un **dispositivo de hardware** (ej., discos, memoria, mouse).

### Abstracción de Dispositivos en UNIX

En sistemas UNIX, cada dispositivo de hardware se representa como un **archivo** por medio de una abstracción, ubicados convencionalmente en el directorio **`/dev`**. Las operaciones básicas sobre estos archivos de dispositivo incluyen:

* **`read` y `write`**: Para leer y escribir "datos crudos" sobre el dispositivo.
* **`ioctl`**: Para configurar el dispositivo o realizar operaciones de control específicas.

Los dispositivos se clasifican principalmente en dos tipos:

* **Dispositivos de bloques**: Permiten acceso aleatorio y gestionan grupos de bloques de datos (ej., discos).
* **Dispositivos de caracter**: Permiten acceso serial, byte a la vez (ej., mouse, sonido).

El kernel identifica un dispositivo mediante su **Major device number** (que agrupa dispositivos del mismo tipo) y **Minor device number** (que identifica una instancia específica). Los archivos de dispositivo pueden crearse manualmente con el comando `mknod`, especificando si es de bloque (`b`) o caracter (`c`), junto con sus números mayor y menor.

### Implementación de Drivers con Módulos

Los drivers se implementan usando **módulos de carga dinámica**. Para que el kernel sepa cómo interactuar con un dispositivo, el driver debe proveer una estructura **`struct file_operations`**. Esta estructura contiene punteros a las funciones que implementan las operaciones básicas sobre el dispositivo, como `read`, `write`, `open` y `release`.

Es crucial que los parámetros que son punteros desde el espacio de usuario hacia el kernel sean manejados con **APIs especiales del kernel** (como **`copy_from_user()`** para copiar datos del espacio de usuario al kernel o **`copy_to_user()`** para copiar datos del kernel al espacio de usuario). Esto garantiza que se acceda de forma segura al espacio de direcciones del proceso que invocó la llamada al sistema, **evitando sobrescribir datos sensibles del kernel**.
