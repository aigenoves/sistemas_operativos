# chroot

**``chroot``** es un mecanismo en sistemas operativos, como Linux, que permite aislar un proceso al cambiar su directorio raíz aparente. Fue introducido en la versión 7 de UNIX en 1979.

- **Propósito:** ``chroot`` hace creer a un proceso que un directorio específico es su directorio raíz (``/``). Esto significa que el proceso y sus hijos no pueden acceder a archivos y comandos fuera de ese "nuevo" directorio raíz.
- **Aislamiento:** Permite ejecutar procesos de forma "aislada". El entorno virtual creado por ``chroot`` se conoce como "jail chroot".
- **Compartición de Kernel:** A pesar del aislamiento, **los procesos dentro de un chroot comparten el mismo kernel** con el sistema operativo anfitrión. Esto implica que no es posible ejecutar instancias de sistemas operativos con un kernel diferente al del sistema base (por ejemplo, Windows sobre Linux).
- **Bibliotecas:** Los procesos ejecutados dentro de un ``chroot`` pueden tener sus propias versiones de bibliotecas.
- **Uso en Contenedores:** Históricamente, ``chroot`` es un precursor de tecnologías de virtualización más modernas. Por ejemplo, Docker utiliza ``chroot`` para establecer el union-filesystem creado como directorio raíz del contenedor. Docker, que es una tecnología de virtualización ligera a nivel de sistema operativo, utiliza ``chroot`` como parte de su mecanismo para aislar el espacio de trabajo de un contenedor.
- **Comando:** Se utiliza el comando ``chroot /new-root-dir comando`` para ejecutar un comando dentro del entorno aislado.

En el contexto de la seguridad, ``chroot`` es uno de los **mecanismos de seguridad en Linux** que permite aislar procesos.