# Resumen del Tema 1: El Kernel de Linux y su Compilación

Este resumen aborda los conceptos fundamentales del kernel de Linux, sus características, tipos de arquitectura, y los pasos detallados para su compilación.

## 1. El Kernel de Linux: Fundamentos

Un **Sistema Operativo (SO)** es un software que actúa como **intermediario entre el usuario y el hardware** de una computadora. Su propósito principal es **facilitar el uso del hardware** y **gestionar los recursos del sistema de manera eficiente**. El **kernel** es la parte fundamental de un SO, residente en la memoria principal, encargado de la **administración de recursos** y la provisión de **servicios esenciales**.

El **kernel de Linux** es **software libre**, aunque puede incluir "blobs binarios", y permite la **compilación de versiones personalizadas**. Está escrito mayormente en **Lenguaje C**, con partes en **Assembler** para instrucciones de bajo nivel, y **Rust** a modo experimental para módulos. Es un kernel **monolítico** que, en sentido estricto, es el propio Sistema Operativo.

Las funciones principales del kernel incluyen:

* Administración de memoria principal.
* Administración del uso de la CPU.
* Administración de procesos.
* Comunicación y concurrencia.
* Gestión de E/S (Input/Output).

El kernel de Linux es responsable de facilitar a los procesos un **acceso seguro al hardware** a través de una interfaz conocida como **"llamadas al sistema" (System Calls)**.

### 1.1. Modos de Ejecución y Soporte de Hardware

La ejecución de código se divide en dos modos:

* **Modo supervisor (o kernel)**: El kernel tiene **acceso completo a todas las instrucciones del procesador**, incluyendo acceso directo al hardware, direccionamiento de memoria y programación de la CPU.
* **Modo usuario**: Los procesos de usuario se ejecutan con **acceso limitado** solo a su propio espacio de direcciones y sin acceso directo al hardware.

El **bit de modo en la CPU** indica el modo actual. El paso de modo usuario a modo kernel solo ocurre a través de una **interrupción (TRAP)**, como una llamada al sistema, y no es un cambio explícito del proceso de usuario. El hardware proporciona apoyo a los SO mediante modos de ejecución, interrupción de reloj y protección de memoria.

### 1.2. Tipos de Arquitecturas de Kernel

Los kernels se pueden clasificar por su arquitectura:

* **Monolítico**: Incluye **todos los servicios del SO en un solo bloque de código** que se ejecuta en modo supervisor. Posee acceso completo a los recursos de hardware, lo que lo hace **eficiente al no requerir cambios de modo**. Aunque es un único bloque, permite un **enfoque modular a través de módulos** que se ejecutan en modo Kernel. Ejemplos incluyen **Linux, Unix y FreeBSD**.
* **Microkernel**: **Minimiza el código** que se ejecuta en modo supervisor, incluyendo solo funciones esenciales como gestión de procesos, comunicación entre procesos y gestión de memoria. Otros servicios (ej., drivers) se ejecutan en espacio de usuario. Son más estables y seguros, pero ofrecen un **rendimiento inferior** debido a los constantes cambios de modo. Ejemplos: **Minix y QNX**.
* **Híbrido**: Combina características de monolíticos y microkernels, con un núcleo más pequeño que un monolítico y algunos servicios en modo núcleo para eficiencia, y otros en modo usuario para seguridad. Ofrecen un equilibrio entre rendimiento y modularidad. Ejemplos: **Windows y MacOS**.
* **ExoKernel**: Provee servicios muy básicos, ofreciendo **acceso directo a recursos de hardware** y dejando las políticas de gestión a bibliotecas de usuario. Buscan reducir los vectores de ataque y errores.

### 1.3. Capacidad de CPU

En sistemas heterogéneos, donde las CPUs tienen diferentes características de rendimiento, la **capacidad de la CPU** mide el rendimiento que una CPU puede alcanzar, normalizada respecto a la CPU más potente del sistema. El *scheduler* de Linux considera estas diferencias mediante la fórmula `capacity(cpu) = work_per_hz(cpu) * max_freq(cpu)`.

## 2. Compilación del Kernel de Linux

La compilación del kernel de Linux sigue un proceso de ocho pasos:

### 2.1. 1. Obtener el código fuente

Generalmente se descarga desde `kernel.org` como un archivo `.tar.xz`. Opcionalmente, se puede verificar la firma criptográfica para asegurar la integridad del archivo.

### 2.2. 2. Preparar el árbol de archivos

Por convención, el código fuente del kernel se guarda en `/usr/src`. Sin embargo, por permisos, se puede descomprimir en el `$HOME` del usuario actual. Es común crear un enlace simbólico llamado `linux` en `/usr/src` que apunte al directorio del código fuente que se está configurando.

### 2.3. 3. Configurar el kernel

La configuración del kernel se realiza mediante el archivo **`.config`**, que define qué se debe compilar. Las configuraciones de kernels ya instalados se pueden ver en `/boot/config-$(uname -r)`. Las herramientas para generar o modificar este archivo incluyen:

* `make config`: Modo texto secuencial, tedioso.
* `make xconfig`: Interfaz gráfica (requiere X).
* `make menuconfig`: **Interfaz de paneles con ncurses**, la más utilizada y recomendada.
Es aconsejable mantener el `.config` para versiones futuras, ya que configurar un kernel desde cero es propenso a errores.

#### 2.3.1. Módulos del Kernel

Los **módulos del kernel** son fragmentos de código que extienden la funcionalidad del kernel y pueden **cargarse o descargarse bajo demanda** sin necesidad de reiniciar el sistema ("en caliente"). Todo su código se ejecuta en **modo Kernel** (privilegiado). Cualquier error en un módulo puede **colgar el SO**. Los módulos permiten un diseño más modular del kernel y se ubican en `/lib/modules/version-del-kernel`. El comando `lsmod` lista los módulos cargados.

La decisión entre compilar el soporte como **"built-in" (dentro del kernel)** o como **"módulo"**:

* **Built-in**: El kernel crece (mayor uso de memoria), lo que puede aumentar el tiempo de arranque. Sin embargo, su utilización es más eficiente al no requerir cargar un adicional en memoria.
* **Módulo**: Utiliza menos memoria al cargarse bajo demanda. Permite modificaciones de drivers sin recompilar todo el kernel.

### 2.4. 4. Parchear el kernel (Opcional)

Se pueden aplicar actualizaciones (parches) utilizando archivos `diff`. Existen parches **no incrementales** (sobre la versión *mainline* anterior) e **incrementales** (sobre la versión inmediatamente anterior). El comando `patch -p1` se usa para aplicar un parche a la raíz del código fuente. La opción `--dry-run` es útil para probar.

### 2.5. 5. Construir el kernel e instalar los módulos

Las herramientas esenciales son **GCC** (compilador de C de GNU), **as** y **ld** (assembler y linker de GNU), y **GNU Make** (para organizar la compilación). **GNU Make** lee instrucciones de un archivo `Makefile` para generar ejecutables.

* Para compilar el kernel, se ejecuta `make`.
* Para compilar los módulos, se usa `make modules`. La opción `make -jX` permite compilación concurrente.
* La imagen compilada del kernel se encuentra en `directorio-del-código/arch/arquitectura/boot/` [No explicitamente en el texto fuente, pero es el resultado final del `make`].
* Se instala el kernel y otros archivos en `/boot` usando `sudo make install`.
* Los módulos compilados se instalan en `/lib/modules/version-del-kernel` usando `sudo make modules install`.

### 2.6. 6. Creación del initramfs

Un `initramfs` es un **sistema de archivos temporal** que se monta durante el arranque del sistema. Contiene ejecutables, drivers y módulos necesarios para iniciar el sistema. Luego del proceso de arranque, este disco se desmonta. Se crea con `mkinitramfs -o /boot/initrd.img-VERSION VERSION`.

### 2.7. 7. Configurar y ejecutar el gestor de arranque

Para el gestor de arranque **GRUB 2**, después de instalar el kernel, se debe ejecutar `update-grub2` (como usuario privilegiado) para que reconozca el nuevo kernel.

### 2.8. 8. Reiniciar el sistema y probar el nuevo kernel

Finalmente, se reinicia el sistema para probar la nueva versión del kernel compilada.
