# Resumen Hilos (Threads)

Este documento resume los conceptos clave sobre hilos, contrastándolos con los procesos y explorando sus tipos, mecanismos de comunicación y sincronización, e implementaciones.

## 1. Procesos vs. Hilos

### Procesos

* En los **primeros Sistemas Operativos**, la unidad básica de utilización de CPU y de asignación de recursos era el **proceso**.
* Cada proceso tenía su **propio espacio de direcciones** y un único flujo de control secuencial.
* Por defecto, los procesos **no comparten memoria de lectura/escritura**. Cuando se crea un nuevo proceso con `fork()`, las páginas de memoria se comparten mediante **Copy-On-Write (COW)**; si se intenta escribir en una página compartida, esta se duplica.
* El **cambio de contexto** entre procesos es costoso y requiere la intervención del sistema operativo para guardar y restaurar el estado completo del proceso.
* La **creación y destrucción** de procesos son operaciones que involucran al sistema operativo y son más lentas.
* Los procesos hijos que terminan y no son "esperados" (mediante `wait()` o `waitpid()`) se convierten en **procesos "zombie"**, que siguen consumiendo una entrada en la tabla de procesos del Kernel. Si el padre termina, los hijos zombie son adoptados por el proceso `init`.

### Hilos (Threads)

* Los hilos son una evolución que permite la **concurrencia dentro del mismo espacio de direcciones**, también conocidos como **Lightweight Processes (LWP)**.
* Un hilo es la **unidad de trabajo** (o de ejecución) dentro de un proceso.
* Los hilos de un mismo proceso **comparten el espacio de direcciones** (código, datos, heap), pero cada hilo tiene su **propio stack** (pila), contexto de procesador y variables locales.
* El **cambio de contexto** entre hilos es significativamente **más rápido** que entre procesos, ya que solo implica el guardado y restauración de registros del procesador y no requiere cambiar el espacio de direcciones. Esto se realiza a menudo sin la intervención explícita del sistema operativo para los hilos de usuario.
* La **creación y destrucción** de hilos son operaciones más rápidas y menos costosas que las de procesos, a menudo gestionadas por una biblioteca en el espacio de usuario.
* Los hilos facilitan la **comunicación y compartición de datos** dentro de una aplicación al tener acceso a la memoria compartida del proceso.
* La **protección** entre hilos debe ser manejada por el desarrollador, a diferencia de los procesos donde el SO garantiza el aislamiento, ya que todos los hilos de un proceso comparten el mismo espacio de direcciones.

## 2. Tipos de Hilos

### Hilos a Nivel de Usuario (ULT - User-Level Threads)

* Son gestionados completamente por una **biblioteca de hilos** en el espacio de usuario, sin que el Kernel tenga conocimiento directo de su existencia individual.
* **Ventajas**:
  * Muy **bajo costo** de creación y cambio de contexto, ya que no requieren llamadas al sistema.
  * Son **independientes del sistema operativo**; una aplicación con ULTs puede ser portable entre diferentes sistemas que no necesariamente soporten hilos a nivel de kernel.
  * Permiten una **planificación personalizada** de los hilos por parte del proceso.
* **Desventajas**:
  * No pueden aprovechar el **paralelismo real** en sistemas con múltiples procesadores (salvo modelos híbridos como M:N), ya que el Kernel solo ve un único LWP o proceso.
  * Si un hilo realiza una **llamada al sistema bloqueante** (ej. lectura de disco), **todo el proceso se bloquea**, afectando a los demás hilos de usuario.
  * Un hilo puede **monopolizar el uso de la CPU** del proceso, ya que el Kernel no interviene en su planificación interna.

### Hilos a Nivel de Kernel (KLT - Kernel-Level Threads)

* La **gestión completa** de estos hilos es realizada directamente por el **Kernel**.
* **Ventajas**:
  * Permiten el **paralelismo real**: el sistema operativo puede programar hilos del mismo proceso para que se ejecuten simultáneamente en diferentes procesadores o núcleos.
  * Una llamada bloqueante de un hilo **no bloquea a todo el proceso**, ya que el Kernel puede programar otros hilos de ese mismo proceso.
* **Implementación en Linux**: La implementación actual de PThreads en Linux (Native POSIX Threads Library - NPTL) funciona como KLTs.

### Lightweight Processes (LWP)

* Son un nivel intermedio. En algunos sistemas, la biblioteca de hilos de usuario multiplexa los ULTs sobre uno o más LWPs, y cada LWP se asigna a un KLT. El Kernel solo "ve" los LWPs.
* Los ULTs pueden ser "ligados" (unidos permanentemente a un LWP, para necesidades de tiempo real) o "no ligados" (multiplexados dinámicamente sobre los LWPs disponibles).

## 3. Comunicación y Sincronización de Hilos

* **Comunicación**: La forma principal es a través de **memoria compartida**, utilizando variables globales o punteros accesibles por todos los hilos del proceso.
* **Sincronización**: Para evitar condiciones de carrera y garantizar la consistencia de los datos compartidos, se utilizan mecanismos como:
  * **Mutexes** (`pthread_mutex_t`): Aseguran que solo un hilo acceda a una sección crítica de código a la vez.
  * **Variables Condicionales** (`pthread_cond_t`): Permiten que los hilos esperen por ciertas condiciones antes de continuar su ejecución.
  * **Barreras** (`pthread_barrier_t`): Sincronizan un grupo de hilos hasta que todos alcanzan un punto específico.
  * **Semáforos** (`sem_init`, `sem_open`): Mecanismos más generales para controlar el acceso a recursos.

## 4. Funciones y Herramientas Comunes

### Funciones PThreads (KLT en Linux)

* `pthread_create(thread, attr, start_routine, arg)`: Crea un nuevo hilo que ejecutará `start_routine`.
* `pthread_join(thread, retval)`: Espera la terminación de un hilo específico. Es importante hacer `join` o `detach` para liberar los recursos del hilo y evitar estados "zombie".
* `pthread_detach(thread)`: Marca un hilo como "detached" para que sus recursos se liberen automáticamente al terminar, sin necesidad de `pthread_join`.
* `pthread_self()`: Retorna el identificador del hilo actual (a nivel PThreads).
* `gettid()`: Retorna el identificador del hilo actual a nivel del sistema operativo, que puede ser diferente al ID de PThreads.

### Funciones GNU Pth (ULT)

* `pth_init()`: Inicializa la biblioteca GNU Pth.
* `pth_spawn()`: Crea un nuevo hilo de usuario.
* `pth_join()`: Espera la terminación de un hilo de usuario.
* `pth_self()`: Retorna el identificador del hilo de usuario actual.

### Hilos en Lenguajes Interpretados

* Lenguajes como **Python** (CPython) y **Ruby** (MRI) suelen usar KLTs, pero con un **Global Interpreter Lock (GIL)**. El GIL asegura que solo un hilo de Python se ejecute a la vez en el intérprete, simplificando la implementación interna pero limitando el paralelismo real para tareas que requieren intensivo uso de CPU. Son más adecuados para tareas IO-Bound.
* El paralelismo real en estos lenguajes se logra típicamente mediante la creación de **procesos** separados.
* **Go** utiliza "Goroutines", que son una implementación de hilos a nivel de usuario M:N, gestionadas por el propio runtime de Go.

### Herramientas de Monitoreo

* `htop`: Permite monitorear el uso de los núcleos de la CPU por procesos y hilos.
* `strace`: Permite monitorear las llamadas al sistema (syscalls) invocadas por un programa, útil para entender su interacción con el Kernel.
* `printk`: Es la función equivalente a `printf` usada dentro del Kernel para imprimir mensajes de depuración.
