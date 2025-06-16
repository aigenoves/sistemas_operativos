# Sistemas Operativos

## Práctica 1

A - Introducción

1. ¿Qué es GCC?  
Según el [Linux Manual Page](https://man7.org/linux/man-pages/man1/gcc.1.html), es un compilador de proyectos C y C++.
2. ¿Qué es make y para que se usa?  
Es una herramienta de construcción automática utilizada para compilar y enlazar proyectos de software. Por defecto funciona leyendo desde un archivo llamado ***Makefile***, aunque se puede indicar que lea desde otro archivo con la opcion -f (o --file). El archivo ***Makefile*** define las dependencias entre archivos y los comandos para generar los objetos.  
3. La carpeta /home/so/practica1/ejemplos/01-make de la ***VM*** contiene ejemplos de uso de **make**. Analice los ejemplos, en cada caso ejecute `make` y luego `make run`(es opcional ejecutar el ejemplo 4, el mismo requiere otras dependencias que no vienen preinstaladas en la ***VM***):

    a. Vuelva a ejecutar el comando `make`. ¿Se volvieron a compilar los programas? ¿Por qué?

    > El comando make detecta que el objetivo `all` depende de `helloworld` y verifica que `helloworld` no exista o que sea más reciente que `helloworld.c`, como no se cumple la primera situación, o sea `helloworld` existe, pero si la segunda situación, o sea `helloworld` es más reciente que `helloworld.c`, el comando `make` no realiza nada.

    b. Cambie la fecha de modificacion de un archivo con el comando `touch`o editando el archivo y ejecute `make`. ¿Se volvieron a compilar los programas? ¿Por qué?

    > Por lo expuesto en la respuesta del punto a, el comando `make` detecta que el archivo `helloworld` existe y es más antiguo que `helloworld.c` entonces se ejecuta la compilación de `helloworld.c` usando gcc.

    c. ¿Por qué *"run"* es un target *"phony"*?

    > Un target *"phony"* en un ***Makefile*** es una regla que no representa un archivo físico en el sistema, solo define una acción a ejecutar (como compliar, limpar, ejecutar test, etc.). Por lo tanto en este caso el target *"run"* simplemente va a ejecutar el programa `helloworld`

    d. En el ejemplo 2 la regla para el target `dlinkedlist.o`no define cómo generar el target, sin embargo el programa se complia correctamente. ¿Por qué es esto?  
        **Nota, si no usás la VM podés descargar los ejemplos desde:
    <https://gitlab.com/unlp-so/codigo-para-practicas>

    > El programa compila correctamente debido a que `make` imcluye reglas predefinidas de tipo implícitas para compliar objetos `.o` a partir fr archivos fuente `.c`. En este caso particular aplica la regla:

    ```makefile
    %.o: %.c
    $(CC) $(CFLAGS) -c $< -o $@
    ```

4. ¿Qué es el *kernel* de GNU/Linux? ¿Cuáles son sus funciones principales deltro del Sistema Operativo?  
   En el año 1983 Richard Stallman inicia el proyecto GNU con el objetivo de crear un sistema operativo libre. Para el año 1990 contaba con casi todos los componentes de un sistema operativo pero aun faltaba que el kernel sea funcional. En el año 1991 Linus Torvalds crea, como un proyecto personal, a Linux siendo este solo un kernel, dado que a GNU solo le faltaba ese componente se decidio fusionar ambos proyectos en lo que hoy se conoce como GNU/Linux un sistema operativo completo y totalmente funcional. Por lo tanto el kernel de GNU/Linux es Linux, un software.
   Dentro de las funciones que tiene el kernel se pueden enumerar las siguientes:  
   - Se encarga de la administración de recursos.  
   - Implementa servicios esenciales:  
        - Manejo de memoria.
        - Manejo de CPU.
        - Administracion (segura y equitativa) de procesos.
        - Facilita el acceso seguro al hardware por parte de los procesos.
        - Comunicación y concurrencia.
        - Gestion de la E/S.
5. Explique brevemente la arquitectura del *kernel* Linux teniendo en cuenta: tipo e *kernel*, módulos, portabilidad, etc.
   1. Linux es un kernel monolícico modular. Monolítico porque todas las funciones básicas (gestion de memoria, procesos, drivers, etc) se ejecutan en *espacio kernel* con acceso total al hardware. **Modular** porque permite cargar/descargar *módulos* dinamicamente (por ejemplo drivers de dispositivos) sin reiniciar al sistema.
   2. Módulos del kernel
      - Drivers y funcionalidades se pueden añadir como módulos (ejemplo driver de placas de video) y tiene como ventaja principal reducir el tamaño del kernel y solo cargar lo necesario. Recordar que el kernel es una pieza de software, código, que se carga en memoría.
   3. Es portable  
        Soporta múltiples arquitecturas.
      - Dado que está escrito principalmente en lenguaje C y assembler y que maneja la abstracción de hardware en capas:
        - arquitectura dependiente (x86, ARM, RISC-V) en esta capa maneja detalles específicos.
        - arquitectura independiente (gestión de procesos, memoria, syscalls) en esta capa maneja detalles en común para todas.
   4. Extensible  
    Los módulos permiten añadir funcionalidades sin tener que ecompilar
   5. Jerarquico  
    Los subsistemas están bien definidos.  

6. ¿Cómo se define el versionado de los kernels Linux en la actualidad?

   Actualmente (desde la versión 3.0) el versionado del kernel de Linux sigue un esquema basado en el tiempo utilizando un formato **A.B.C**.  

   **Formato del versionado A.B.C:**

   - **Versión (A):** Desde la implementación de este sistema de versionado Linus Torvalds decidió que este número se incrementaría simplemente cuando el número **"B"** se volviera demasiado grande, sin un significado técnico particular. Por ejemplo se paso de la versión 2.6 a la 3.0 y de la 4.20 a la 50.

   - **Lanzamiento principal o Major Release (B):** Este número identifica a una nueva versión principal del kernel. Estos lanzamientos, conocidos como **`mainline`**, son gestionados directamente por Linus Torvalds y se publican aproximadamente cada 9 o 10 semanas. Un nuevo número **"B"** indica una nueva versión con nuevas caracteristicas, controladores y mejoras.

   - **Revisión, Patch/Stable Release (C):**
   Este número corresponde a las actualizaciones de corrección de errores (bug fixes) y parches de seguridad para una versión principal. Una vez que se libera una versión principal (por ejemplo la 6.9), pasa a un estado "estable". Se publican revisiones incrementales (6.9.1, 6.9.2, etc.) a medida que se solucionan problemas.

   **Tipos de lanzamientos y ciclo de vida**

   El desarrollo y mantenimiento del kernel se organiza en varios tipos de ramas o árboles.

   1. **Mainline (principal):**  
   Es la rama de desarrollo principal, donde se introducen todas las nuevas funcionalidades. Antes de un lanzamiento (como 6.10), se publican las versiones candidatas.
       - **Release Candidate (rc)**:  
       Durante el ciclo de desarrollo de 9 o 10 semanas, Linus Torvalds publica versiones candidatas o **"release candidates"** (por ejemplo, 6.10-rc1, 6.10-rc2, etc.). Estas versiones no se consideran estables y están destinadas a que la comunidad las pruebe masivamente para encontrar y corregir errores antes de lanzar la versión final.
   2. **Stable (estable):**  
   Una vez que s epublica una versión `mainline` (por ejemplo la 6.9), esta se considera "estable". A partir de ese momento, solo recibirá correcciones de errores (incrementando el número `C`). Estas actualizaciones se publican según sea necesario, generalmente una vez por semana. La mayoría de las versiones estables solo se mantienen hasta que se publica la siguiente versión `mainline`.
   3. **Longterm Support (LTS) o soporte a largo plazo:**  
   Algunas versiones estables son designadas como LTS. Estas versiones reciben manteniendo durante un período mucho más largo, típicamente de 2 a 6 años o incluso más. Son ideales para sistemas en producción y para distribuciones de Linux que necesitan una base sólida del kernel (como Debian, Ubuntu LTS, etc.). La elección de qué kernel se convierte en LTS depende de varios factores, incluyendo la decisión de los mantenedores y las necesidades de la industria.  

    **Kernels de distribución**  
    Muchas ditribuciones de Linux (como Ubintu, Fedora o Arch Linux) compilan y mantienen sus propias versiones de l kernel. A menudo, toman una versión estable o LTS y le añaden sus propios parches y configuraciones específicas. Por eso, al verificar la version del kernel en un sistema (con el comando `uname -r`), es común ver sufijos adicionales que identifican la compilación de la distribución (por ejemplo, `6.8.0-35-generic` en Ubuntu). Estos no forman parte del versionado oficial de kernel.org.

7. ¿Cuáles son los motivos por los que un usuario/a GNU/Linux puede querer re-compilar el kernel?

   Un usuario/a de GNU/Linux puede querer recompilar el kernel por varias razones, principalmente para personalizarlo y adaptarlo a necesidades o escenarios específicos.
   - **Personalización y Adaptación a Necesidades Específicas:**

     La compilación del kernel permite al usuario **personalizarlo** para incluir solo las funcionalidades deseadas o requeridas. Esto incluye dar soporte a **sistemas de archivos específicos** (como BTRFS) o a **dispositivos particulares** (como dispositivos de Loopback), que pueden no estar activados por defecto en un kernel precompilado genérico.

   - **Optimización de Rendimiento y Uso de Recursos:**

     - Al compilar el kernel, se puede elegir si el soporte para cierta funcionalidad será **built-in** (parte del kernel) o como un **módulo** (cargable bajo demanda).
     - Si se opta por soporte **built-in**, se puede lograr una utilización más eficiente y un acceso directo a la funcionalidad, aunque el kernel crecerá en tamaño y podría incrementar el tiempo de arranque. Por otro lado, si se usa como **módulo**, el uso de memoria es menor, ya que se carga solo cuando se necesita, y permite extender la funcionalidad sin reiniciar el sistema.
     - Esta capacidad de selección permite reducir el tamaño del kernel y optimizar su huella de memoria, lo que puede resultar en t**iempos de arranque más rápidos** y un **menor consumo de recursos** al eliminar código innecesario.

   - **Soporte para Hardware Específico o Nuevo/Antiguo:**

     Si el kernel estándar de una distribución no incluye el **soporte para un hardware particular** (por ejemplo, un nuevo dispositivo o uno muy antiguo/raro) o un **driver específico**, recompilar el kernel permite **agregar el controlador necesario**.

   - **Aplicación de Parches y Correcciones:**

     Es posible que un usuario necesite aplicar **parches** sobre una versión base del kernel. Estos parches, que se basan en archivos `diff`, pueden agregar **funcionalidad** (como nuevos drivers), corregir **errores menores** o solucionar **vulnerabilidades de seguridad** que aún no han sido incorporadas en las versiones oficiales o empaquetadas por la distribución.

   - **Experimentación y Aprendizaje:**

     Como parte de un objetivo educativo o práctico, recompilar el kernel permite c**omprender los pasos del proceso de compilación** y los conceptos del kernel en general. Esto convierte la recompilación en una **actividad de aprendizaje** valiosa para profundizar en el funcionamiento del sistema operativo.

8. ¿Cuáles son las distintas opciones y menús para realizar la configuración de opciones de compilación de un kernel? Cite diferencias, necesidades (paquetes adicionales de  software que se pueden requerir), pro y contras de cada una de ellas.

    Para configurar las opciones de compilación de un kernel Linux, el proceso se centra en la creación o modificación de un archivo llamado **`.config`**. Este archivo es crucial ya que contiene las instrucciones que el kernel debe compilar. La configuración de kernels ya instalados puede consultarse en `/boot/config-$(uname -r)`.
    Existen tres interfaces principales para generar o modificar este archivo **`.config`**, cada una con sus propias características, requisitos, ventajas y desventajas:

    - **make config:**

        - **Descripción:** Es un modo **texto secuencial**. Esto significa que el usuario interactúa con una serie de preguntas una tras otra, respondiendo "sí", "no" o "módulo" para cada opción de configuración [Información no explícitamente en las fuentes, pero implícita por "modo texto secuencial" y el contexto de configuración del kernel].

        - **Diferencias/Pros y Contras:** Se considera **tedioso** debido a su naturaleza secuencial y la gran cantidad de opciones disponibles en un kernel moderno. No es la opción más recomendada para configurar un kernel desde cero por ser propensa a errores.

        - **Necesidades (paquetes adicionales):** No requiere paquetes gráficos adicionales; solo las herramientas de compilación básicas como GNU Make.

    - **make xconfig:**
        - **Descripción:** Ofrece una i**nterfaz gráfica** utilizando un sistema de ventanas. Esto permite una navegación más intuitiva y visual de las opciones del kernel.

        - **Diferencias/Pros y Contras:** Proporciona una experiencia de usuario más amigable que `make config` al permitir el uso del mouse y una organización visual de las opciones. Sin embargo, su principal desventaja es que **no todos los sistemas tienen un entorno X (sistema de ventanas) instalado**, lo que puede ser un requisito no cumplido en servidores o entornos mínimos.

        - **Necesidades (paquetes adicionales):** Requiere un sistema X (X Window System) en funcionamiento y bibliotecas asociadas para la interfaz gráfica.

    - **make menuconfig:**

        - **Descripción:** Es el modo más utilizado. Utiliza **ncurses**, una librería que permite generar una **interfaz con paneles desde la terminal**. Esta interfaz es basada en texto, pero organizada en menús y submenús navegables con el teclado, ofreciendo un equilibrio entre simplicidad y funcionalidad.

        - **Diferencias/Pros y Contras:** Es considerablemente menos tedioso que `make config` y no requiere un entorno gráfico como `make xconfig`. Permite navegar las opciones de manera jerárquica y buscar configuraciones específicas. Al igual que las otras herramientas, ayuda a automatizar el proceso de configuración y reducir la propensión a errores al configurar un kernel desde cero.

        - **Necesidades (paquetes adicionales):** Requiere la librería ncurses, la cual suele estar instalada por defecto en la mayoría de las distribuciones Linux o es fácil de instalar.

    **Consejos generales para la configuración del kernel**

    - **Archivos `.config` existentes:** Es ideal **mantener el archivo `.config`** de un kernel anterior o de la configuración del kernel actualmente instalado (generalmente en `/boot/config-$(uname -r)`) para no tener que configurar todo desde cero. Cada nueva versión del kernel puede valerse de un `.config` anterior.

    - **Almacenamiento:** Por convención, es recomendable almacenar la imagen compilada del kernel junto con su `.config` en el directorio `/boot`.

    - **Módulos vs. Built-in:** Durante la configuración, se debe decidir si el soporte para una funcionalidad específica será **built-in** (parte integral del kernel) o como un **módulo** (cargable bajo demanda).

        - **Built-in:** El kernel crece en tamaño, mayor uso de memoria, puede incrementar el tiempo de arranque. Sin embargo, su utilización es más eficiente, con acceso directo al no tener que cargar un adicional en memoria.
        - **Módulo:** Permiten extender la funcionalidad del kernel "en caliente" sin reiniciar el sistema. Se cargan bajo demanda, resultando en un menor uso de memoria. Si hay una modificación de un driver, solo se modifica el módulo y no todo el código del kernel. No obstante, cualquier error en el módulo puede colgar el sistema operativo.
        - **Soporte adicional:** En las prácticas específicas, puede ser necesario configurar soporte para sistemas de archivos como **BTRFS** o para **dispositivos de Loopback**.
        - **Parches:** Aplicar **parches** al kernel utilizando archivos `diff` (ver la ejecicio taller de la práctica 1). Esto permite agregar funcionalidad, corregir errores menores o aplicar actualizaciones sin descargar todo el código de una nueva versión. Se aplican con el comando `patch`, por ejemplo: `xzcat ../patch-6.13.7.xz | patch -p1`.

9. Indique qué tarea realiza cada uno de los siguientes comandos durante la tarea de configuración/compilación del kernel:

    - **make menuconfig:**
        - Este comando se utiliza para **configurar el kernel Linux**.
        - Ofrece una **interfaz con paneles desde la terminal** utilizando la librería `ncurses`.
        - Es el modo **más utilizado** para la configuración del kernel.
        - Permite **crear un archivo `.config` con las directivas de compilación** y automatiza el proceso de configuración, lo que es crucial ya que configurar un kernel desde cero puede ser tedioso y propenso a errores.
        - El archivo `.config` contiene las instrucciones de qué es lo que el kernel debe compilar. Se recomienda mantener y reutilizar el archivo `.config` de un kernel anterior para evitar configurar todo desde cero en nuevas versiones.
    - **make clean:**
        - Esta es una **buena práctica** al definir los targets en un `Makefile`.
        - **Borra los binarios y archivos intermedios generados** durante el proceso de compilación. En el contexto del kernel, esto asegura una compilación limpia.
    - **make:**
        - El comando `make` busca el archivo `Makefile` (dentro del directorio del código fuente del kernel), interpreta sus directivas y **compila el kernel**.
        - Este proceso puede durar mucho tiempo dependiendo del procesador del sistema.
        - **Parámetro `-j`:** Permite ejecutar la compilación con **múltiples *jobs* (tareas) concurrentes**. Por ejemplo, `make -j 8` ejecutará 8 trabajos simultáneamente. Esto puede acelerar significativamente el tiempo de compilación al aprovechar los múltiples núcleos del procesador.
    - **make modules**:
        - utilizado en antiguos kernels, actualmente no es necesario.
        - **Compila todos los módulos necesarios** para satisfacer las opciones que fueron seleccionadas como módulo durante la configuración.
        - Actualmente, la tarea de `make modules` suele estar **incluida en la compilación del kernel con el comando `make`**.
    - **make modules_install:**
        - Una vez que los módulos han sido compilados, este comando **los instala en el directorio `/lib/modules/version_del_kernel`**.
        - Es una regla definida en el `Makefile` del kernel que ubica los módulos en su directorio correspondiente.
    - **make install:**
        - **Instala la imagen del kernel y otros archivos en el directorio `/boot`.**
        - Después de la compilación, la imagen del kernel se encuentra ubicada en `directorio-del-código/arch/arquitectura/boot/`. Este comando automatiza su copia y la de otros archivos relevantes (como `System.map` y `.config`) a `/boot`.

10. Una vez que el kernel fue compilado, ¿dónde queda ubicada su imagen? ¿dónde debería ser reubicada? ¿Existe algún comando que realice esta copia en forma automática?

    Una vez que el kernel ha sido compilado, su imagen queda ubicada en el directorio del código fuente, específicamente en la ruta `directorio-del-código/arch/arquitectura/boot/`.

    Luego de la compilación, el siguiente paso es **instalar el kernel y otros archivos en el directorio `/boot`**. Por convención, es **recomendable almacenar en el directorio `/boot` la imagen compilada del kernel junto con su archivo `.config`.**

    Existe un comando que realiza esta copia de forma automática: **`sudo make install`**. Esta es una regla definida en el `Makefile` del kernel que se encarga de ubicar la imagen del kernel y otros archivos relevantes en el directorio `/boot`.

11. ¿A qué hace referencia el archivo initramfs? ¿Cuál es su funcionalidad? ¿Bajo qué condiciones puede no ser necesario?

    El archivo initramfs (initial RAM filesystem) hace referencia a un **sistema de archivos temporal que se monta durante el arranque del sistema**.

    Su funcionalidad principal es **contener los ejecutables, *drivers* y módulos necesarios para lograr iniciar el sistema**. Una vez que el sistema ha arrancado completamente, este disco temporal se desmonta.

    El uso de initramfs puede evitarse si:
    - El **kernel tiene integrados directamente todos los drivers** necesarios para montar el sistema de archivos raíz.

    - El sistema de archivos raíz **no depende de configuraciones especiales**, como:
      - No se usa LVM, RAID ni cifrado.
      - El disco raíz está directamente accesible (por ejemplo, un disco SATA sin módulos especiales).

    - El gestor de arranque puede pasar el control directamente al kernel con la ubicación del sistema de archivos raíz ya montable.

    Esto se da principalmente en sistemas embebidos o configuraciones muy simples y controladas.

12. ¿Cuál es la razón por la que una vez compilado el nuevo kernel, es necesario reconfigurar el gestor de arranque que tengamos instalado?

    La razón fundamental es que el gestor de arranque (comúnmente GRUB) mantiene una lista de los kernels disponibles para iniciar el sistema. Si se compila e instala un nuevo kernel, esta nueva imagen no será automáticamente reconocida por el gestor de arranque. La reconfiguración le permite al gestor de arranque **actualizar su base de datos o archivo de configuración** para incluir el nuevo kernel como una opción de arranque.

    Por ejemplo, después de instalar el kernel con `make install` (que copia la imagen del kernel y otros archivos al directorio `/boot`) y los módulos con `make modules_install` (que los ubica en `/lib/modules/version_del_kernel`), para que el gestor de arranque GRUB 2 reconozca el nuevo kernel, se debe ejecutar el comando `sudo update-grub2`. Este comando actualiza la configuración de GRUB para que el nuevo kernel aparezca como una opción al iniciar el sistema, lo que permite al usuario seleccionarlo y probarlo.

13. ¿Qué es un módulo del kernel? ¿Cuáles son los comandos principales para el manejo de módulos del kernel?

    Un **módulo del kernel** es un **fragmento de código que puede cargarse y descargarse en el mapa de memoria del sistema operativo (Kernel) bajo demanda**. Estos módulos permiten **extender la funcionalidad del Kernel en "caliente", es decir, sin la necesidad de reiniciar el sistema**. Todo su código se ejecuta en modo Kernel (privilegiado).

    La existencia de módulos permite que el kernel se desarrolle bajo un **diseño más modular**. Si, por ejemplo, hay una modificación en un driver, solo se necesita modificar el módulo y no todo el código del kernel. Además, los módulos se cargan bajo demanda, lo que resulta en una **menor utilización de memoria**. Sin embargo, es importante tener en cuenta que **cualquier error en un módulo puede colgar el sistema operativo**.
    Los módulos disponibles se ubican convencionalmente en el directorio `/lib/modules/version_del_kernel`.

    Los **comandos principales para el manejo de módulos del kernel** son:

    - **`lsmod`:** Este comando lista los módulos cargados. Es equivalente a cat /proc/modules.
    - **`rmmod [módulos]`:** Se utiliza para descargar uno o más módulos.
    - **`modinfo [módulo]`:** Este comando muestra información sobre el módulo especificado.
    - **`insmod [módulo] [opciones]`:** Intenta cargar el módulo especificado.
    - **`depmod`**: Permite calcular las dependencias que deben respetarse al cargar y descargar módulos. Por defecto, depmod -a escribe las dependencias en el archivo /lib/modules/version/modules.emp.
    - **`modprobe [módulo] [opciones]`:** Emplea la información de dependencias generada por depmod y la información de /etc/modules.conf para cargar el módulo especificado.

14. ¿Qué es un parche del kernel? ¿Cuáles son las razones principales por las cuáles se deberían aplicar parches en el kernel? ¿A través de qué comando se realiza la aplicación de parches en el kernel?

    Un **parche del kernel** es un **mecanismo que permite aplicar actualizaciones sobre una versión base** del código fuente del kernel. Estos parches se basan en **archivos `diff` (archivos de diferencia)**, los cuales indican qué contenido debe agregarse y qué debe quitarse del código.

    Las razones principales por las cuales se deberían aplicar parches en el kernel son:

    - **Agregar funcionalidad**, como nuevos drivers.
    - **Realizar correcciones menores** al código.
    - En ocasiones, puede resultar **más sencillo descargar un archivo de diferencia y aplicarlo en lugar de descargar todo el código** de una nueva versión del kernel. Esto sugiere una ventaja en términos de eficiencia para mantener el kernel actualizado.

    La aplicación de parches en el kernel se realiza a través del comando `patch`. Un ejemplo del comando para aplicar un parche es el siguiente: `$ cd linux; xzcat ../patch-6.13.7.xz | patch -p1`.

    El parámetro `--dry-run` es útil para probar la aplicación del parche sin realizar cambios reales en el código.

15. Investigue la característica Energy-aware Scheduling incorporada en el kernel 5.0 y explique brevemente con sus palabras.
    1. ¿Qué característica principal tiene un procesador ARM big.LITTLE?
        La característica principal de un procesador ARM big.LITTLE es que está compuesto por **CPUs con diferentes microarquitecturas y capacidades de rendimiento**. Específicamente, incluyen:

        - **CPUs "big":** que están más orientadas al rendimiento, con más etapas de pipeline, cachés más grandes y predictores más inteligentes. Generalmente, pueden alcanzar OPPs (puntos de rendimiento operativo) más altos.
        - **CPUs "LITTLE":** que son de menor rendimiento y suelen ser más eficientes energéticamente en comparación con las "big" CPUs. Esta combinación permite al sistema equilibrar el rendimiento y la eficiencia energética, utilizando las CPUs "big" para cargas de trabajo exigentes y las "LITTLE" para tareas menos intensivas.

    2. En un procesador ARM big.LITTLE y con esta característica habilitada. Cuando se despierta un proceso ¿a qué procesador lo asigna el scheduler?

        En un sistema con procesadores ARM big.LITTLE y con la programación consciente de la capacidad habilitada (que sirve de base para EAS), el scheduler CFS utiliza un **criterio de "idoneidad de capacidad" (capacity fitness criterion)**. Este criterio busca una CPU donde la utilización de la tarea (p) sea menor que la capacidad de la CPU. Además, se considera el valor "clamp" de la tarea, que permite a los usuarios (a través de `uclamp`) especificar un rango de utilización mínimo y máximo para una tarea, influyendo así en la selección de la CPU. La fórmula que busca satisfacer el scheduler para la selección de CPU es `clamp(task_util(p), task_uclamp_min(p), task_uclamp_max(p)) < capacity(cpu)`.

    3. ¿A qué tipo de dispositivos opinás que beneficia más esta característica?

        En mi opinión, esta característica beneficia principalmente a dispositivos donde la gestión de energía es crítica y las cargas de trabajo son variables, desde muy ligeras hasta muy intensivas. Esto incluye:

        - Dispositivos móviles (smartphones, tablets, wearables)
        - Laptops y ultrabooks con arquitecturas híbridas
        - Dispositivos de borde (Edge Computing)

    Ver <https://docs.kernel.org/scheduler/sched-energy.html>

16. Investigue la system call memfd_secret() incorporada en el kernel 5.14 y explique brevemente con sus palabras

    1. ¿Cuál es su propósito?

        El propósito principal de `memfd_secret()` es **permitir que un proceso en el espacio de usuario (user-space) cree una región de memoria que sea inaccesible para cualquier otra entidad, incluyendo el propio kernel**. La funcionalidad de esta llamada al sistema es la de asegurar un área de memoria "secreta" que protege los datos sensibles de accesos no autorizados.
    2. ¿Para qué puede ser utilizada?
        Esta `system call` puede ser utilizada para almacenar cualquier tipo de datos que requieran una alta confidencialidad y que no deban ser expuestos a otras partes del sistema, ni siquiera al kernel. Algunos de los usos mencionados en las fuentes incluyen:

        - **Almacenamiento de claves criptográficas**.
        - **Otros datos sensibles** que deban permanecer ocultos.
        - **Claves de sesión de VPN**, tokens de acceso para TPMs (Trusted Platform Modules) de hardware, cookies de sesión para sudo y otras credenciales efímeras pero importantes.
        - Para propósitos de **DRM (Digital Rights Management)**, como las claves de descifrado de Netflix o módulos como Widevine, buscando impedir que el usuario inspeccione el código que se ejecuta en su máquina. Esto es particularmente relevante en Android y ChromeOS, donde los proveedores de dispositivos podrían requerir esta funcionalidad.
        - Como una **capa de "defensa en profundidad" (defense in depth)** contra ataques de "buffer overread" simples o fugas de información del kernel que no implican una ejecución arbitraria de código privilegiado. Busca hacer más difícil la construcción de ciertos ataques, aunque no proporciona garantías absolutas de seguridad.

    3. ¿El kernel puede acceder al contenido de regiones de     memoria creadas con esta system call?

        En teoría y por diseño, el kernel no puede acceder directamente al contenido de las regiones de memoria creadas con memfd_secret()

    El siguiente artículo contiene bastante información al respecto: <https://lwn.net/Articles/865256/>
