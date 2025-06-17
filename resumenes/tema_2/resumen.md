# Resumen: Llamadas al Sistema en GNU/Linux

Las **llamadas al sistema (syscalls)** son el mecanismo fundamental a través del cual un proceso en el espacio de usuario solicita y accede a los **servicios proporcionados por el núcleo (kernel)** del sistema operativo. Estos servicios incluyen, pero no se limitan a, la gestión de memoria, control de la CPU, manejo de archivos, administración de dispositivos, y comunicación inter-procesos.

## La Interfaz `libc` y POSIX

En sistemas **Unix y GNU/Linux, la Interfaz de Programación de Aplicaciones (API) principal que expone estos servicios es `libc`** (la biblioteca estándar de C). `libc` actúa como un **intermediario entre las aplicaciones de usuario y las llamadas al sistema del kernel**, proveyendo las librerías estándar de C. Gran parte de la funcionalidad de `libc` y las llamadas al sistema está **definida por el estándar POSIX**, cuyo propósito es ofrecer una interfaz común para lograr la portabilidad del código. Generalmente, cada función de la API de `libc` se corresponde con una llamada al sistema subyacente, permitiendo que el desarrollador interactúe con la API en lugar de directamente con el kernel.

## Proceso de Invocación de una Llamada al Sistema

La invocación de una llamada al sistema sigue una serie de pasos:

*   Los **parámetros necesarios para la llamada se colocan en la pila** del proceso de usuario.
*   Se invoca una función de `libc` (conocida como *wrapper* de llamada al sistema), que se encarga de indicar el **número de la llamada al sistema** que se desea ejecutar y sus argumentos.
*   Esta función ejecuta una **interrupción por software, también llamada TRAP**, para cambiar el modo de ejecución del procesador de **modo usuario a modo kernel**.
*   Una vez en modo kernel, el sistema operativo utiliza el número de la llamada al sistema (típicamente cargado en un registro, como EAX en x86 32-bit o RDI en AMD64) para buscar en la **tabla de llamadas al sistema** y determinar qué función *handler* del kernel debe ejecutar.
*   El kernel entonces ejecuta el código correspondiente a la llamada, lo que puede implicar acceso a dispositivos de hardware o realizar operaciones privilegiadas.
*   Los parámetros pasados a las llamadas al sistema deben manejarse con extremo cuidado, ya que se configuran en el espacio de usuario. Es crucial que los punteros no apunten al espacio del kernel para evitar problemas de seguridad, y el kernel utiliza APIs especiales (como `copy_from_user()`) para acceder de forma segura a los datos en el espacio de usuario.

Las instrucciones específicas para invocar llamadas al sistema son **dependientes de la arquitectura de la CPU**. Por ejemplo, Intel x86 de 32 bits usa la instrucción `int 0x80`, mientras que AMD64/x86_64 usa la instrucción `syscall`.

## Desarrollo y Personalización de Llamadas al Sistema

Desarrollar una nueva llamada al sistema en GNU/Linux implica:

*   Asignarle un **número único** a la nueva syscall.
*   Añadir una entrada a la **tabla de llamadas al sistema** del kernel (por ejemplo, en archivos como `syscall_64.tbl`).
*   Respetar las convenciones del kernel, como el uso de prefijos (`sys_`, `__x64_sys_`) para las funciones *handler*.
*   Utilizar la macro `asmlinkage` para instruir al compilador sobre cómo deben pasarse los parámetros a la función del kernel (por pila en x86 de 32 bits, por registros en otras arquitecturas).
*   Implementar la lógica de la llamada al sistema utilizando funciones internas del kernel, como **`printk` para mensajes de depuración** (en lugar de `printf`, que es una función de `libc`).
*   Finalmente, requiere **recompilar el kernel** para que los cambios surtan efecto.

## Herramientas para el Monitoreo

La utilidad **`strace`** es una herramienta esencial para los desarrolladores, ya que permite **reportar las llamadas al sistema invocadas por un proceso**, facilitando la depuración y el análisis del comportamiento de las aplicaciones.

El diseño del kernel Linux es predominantemente **monolítico**, lo que significa que la mayoría de sus componentes (scheduler, drivers, gestor de memoria) están **vinculados en un único binario y comparten el mismo espacio de memoria**. Si bien esto ofrece eficiencia al no requerir cambios de modo constantes, un error en un driver puede provocar un fallo de todo el sistema (Kernel Panic en Unix). A pesar de debates históricos sobre arquitecturas de microkernel (como Minix), el diseño monolítico de Linux ha prevalecido, aunque con un enfoque modular a través de la carga dinámica de módulos del kernel para extender su funcionalidad sin necesidad de reiniciar.