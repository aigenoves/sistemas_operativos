# Llamadas al Sistema: Interacción Kernel-Usuario

Las llamadas al sistema (**System Calls**) son el mecanismo fundamental que un proceso que se ejecuta en el espacio de usuario utiliza para solicitar un servicio directamente al **Kernel del Sistema Operativo (SO)**.

- **Propósito y Rol del Kernel**

  - Un proceso de usuario ejecuta sus propias instrucciones en un entorno controlado y limitado, sin acceso directo al hardware ni a todo el espacio de direcciones.
  - El **Kernel** es el responsable de garantizar que los procesos de usuario se ejecuten de forma controlada y sin interferencias .
  - Cuando un proceso de usuario necesita acceder a funcionalidades o recursos protegidos por el SO (como dispositivos de hardware, gestión de memoria, CPU, archivos, o instrucciones privilegiadas), lo hace a través de una llamada al sistema.
  - El SO actúa como un "servidor" que recibe y atiende los requerimientos de sus "clientes" (los procesos).

- **Modos de Ejecución**
  - El Kernel se ejecuta en **modo supervisor o privilegiado (Ring 0 en x86)**, teniendo acceso completo a las instrucciones y recursos del hardware.
  - Los procesos de usuario se ejecutan en modo **usuario (Ring 3 en x86)**, con un conjunto limitado de instrucciones y acceso solo a su propio espacio de direcciones.
  - Una llamada al sistema es la **única forma de pasar de modo usuario a modo Kernel.**

- **Funcionamiento de una Llamada al Sistema**
  
  1. **Preparación de Parámetros:** El proceso de usuario coloca los parámetros necesarios para la función en la pila.

  2. **Invocación de la Función Wrapper:** Se invoca una función implementada en una **librería de usuario** (**como libc**) que actuará como "wrapper" de la llamada al sistema.
  
  3. **Cambio de Modo (Trap/Interrupción por Software):** Esta función `wrapper` ejecuta una instrucción especial (`int 0x80` en x86 de 32 bits, `syscall` en AMD64/x86_64, `svc` en ARM) que genera una interrupción por software (o "trap"). Esta interrupción cambia el modo de ejecución de usuario a Kernel y transfiere el control al SO.

  4. Identificación y Ejecución en el Kernel: Una vez en modo Kernel, el SO identifica la llamada al sistema solicitada (generalmente a través de un número de syscall en un registro, como `EAX` en x86 de 32 bits o `RAX` en x86_64) en una **tabla de llamadas al sistema** (`syscall table`). Luego, ejecuta la función manejadora (`handler function`) correspondiente en el contexto del proceso que la invocó.

- **Interfaces y Portabilidad**
  - Los SOs proveen una Interfaz de Programación de Aplicaciones (**API**) que los procesos utilizan.
  - En UNIX y GNU/Linux, la API principal para la invocación de System Calls es `libc`.
  - Gran parte de la funcionalidad de `libc` y las System Calls está definida por el **estándar POSIX**, que busca proveer una interfaz común para lograr portabilidad. Generalmente, cada función de la API se corresponde con una System Call.

- **Manejo de Parámetros y Seguridad**
  - Los parámetros de una System Call se configuran en el espacio de usuario y deben manejarse con cuidado.
  - El Kernel no puede asumir que los parámetros son correctos.
  - Por seguridad, los punteros pasados como argumentos no pueden apuntar directamente al espacio del Kernel; de lo contrario, se podría sobrescribir datos sensibles o causar un Kernel Panic.
  - El Kernel utiliza APIs especiales (como `copy_from_user()` y `copy_to_user()`) para garantizar que se acceda de forma segura solo al espacio de direcciones del proceso invocador.

- **Categorías de System Calls**
  - Control de Procesos.
  - Manejo de Archivos.
  - Manejo de Dispositivos.
  - Mantenimiento de Información del Sistema.
  - Comunicaciones.

- **Ejemplos de System Calls**
  - `read()`: Para leer archivos o dispositivos.
  - `chmod()`: Para cambiar permisos.
  - `gettimeofday()`: Para obtener la hora del sistema.
  - `getpid()`: Para obtener el PID del proceso actual.
  - `fork()`: Para crear un nuevo proceso. En Linux, puede ser una especialización de `clone()`.
  - `clone()`: Una llamada al sistema más flexible para crear nuevas entidades de ejecución (procesos o hilos) con control sobre los recursos compartidos, siendo la base para hilos (`pthread_create` usa `syscall clone3()`) y el aislamiento en namespaces.
  - `unshare()`: Para agregar el proceso actual a un nuevo namespace.
  - `setns()`: Para agregar el proceso actual a un namespace existente.

- **Desarrollo de System Calls**
  - Requiere identificar la syscall con un número único y agregar una entrada a la tabla de llamadas al sistema del kernel.
  - Los parámetros a las syscalls deben pasarse a través de la pila, para lo cual se usa la macro `asmlinkage`.
  - El código de la syscall debe ser definido en el árbol de archivos del kernel.
  - Implementar una nueva syscall implica recompilar el Kernel.

Las llamadas al sistema son la **puerta de entrada controlada y segura** para que las aplicaciones de usuario interactúen con las funcionalidades y recursos privilegiados del sistema operativo, garantizando el aislamiento y la estabilidad del sistema.
