# A - Introducción

## 1. Defina política y mecanismo

En el ámbito de los sistemas operativos, las políticas y los mecanismos son conceptos fundamentales que se distinguen para permitir flexibilidad en el diseño y la implementación.

   Definiciones:

- Las **políticas** (el "qué"):
  - Definen **lo que se quiere hacer**.
  - Se basan en los objetivos del sistema.
  - Rara vez incluyen configuraciones específicas.
  - Pueden asociarse a los "papeles" o roles.
- Los **mecanismos** (el "cómo"):
  - Definen **cómo se implementan** las políticas.
  - En este punto, aparecen las configuraciones y las implementaciones reales.
  - Existen diferentes mecanismos que pueden utilizarse para cumplir una misma política.

  La separación entre **políticas** y **mecanismos** es crucial porque garantiza la **flexibilidad** de un sistema. Por ejemplo, una política de seguridad puede ser "solo los usuarios autorizados pueden acceder a ciertos archivos". Los mecanismos para implementar esto podrían ser permisos de acceso en el sistema de archivos (como los permisos UGO en Linux) o listas de control de acceso (ACLs).

  En un contexto más amplio, la **protección** se refiere a los **mecanismos específicos** que el sistema operativo implementa para resguardar la información y controlar el acceso de procesos o usuarios a los recursos. Por otro lado, la **seguridad** es un concepto más general que mide la confianza en la preservación de la integridad del sistema y sus datos, lo cual puede verse como la **política** general a alcanzar.

  Un ejemplo concreto de cómo se aplican las políticas y mecanismos es **AppArmor**, una implementación de Linux Security Module (LSM). AppArmor permite al administrador especificar el dominio de actividades que un programa puede realizar mediante el desarrollo de un **perfil de seguridad**. Este perfil, que es una lista de archivos a los que el programa puede acceder y las operaciones que puede realizar, representa la **política** de seguridad para esa aplicación. Los **mecanismos** incluyen la operación de AppArmor en modo `enforcing` (aplicando las reglas y bloqueando acciones no autorizadas) o en modo `complain` (registrando las acciones pero sin bloquearlas, lo que permite ajustar los perfiles).

## 2. Defina objeto, dominio y right

### Objeto

En un sistema informático, un **objeto** es cualquier recurso que necesita protección. Estos pueden ser recursos de **hardware** (como la CPU, memoria o dispositivos de E/S) o de **software** (como archivos, programas o semáforos). Cada objeto posee un **identificador único** que permite referenciarlo, y los procesos pueden realizar un conjunto finito de operaciones sobre ellos.

### Dominio

Un **dominio de protección** es un conjunto de pares (objeto, derecho). Cada par dentro de un dominio especifica un objeto y las operaciones permitidas sobre él.

Un dominio puede estar asociado a un **usuario**, un **proceso** o un **procedimiento**. En sistemas como Unix, el dominio se define por el **UID (User ID)** y el **GID (Group ID)**, lo que implica que procesos con el mismo UID/GID comparten el mismo dominio de acceso a recursos.

El principio de "**need-to-know**" (o POLA, Principle of Least Authority) sugiere que los procesos solo deben acceder a los objetos que necesitan y con los derechos mínimos indispensables para su tarea. La relación entre un proceso y su dominio puede ser **estática** (fija durante su ciclo de vida) o **dinámica** (pudiendo cambiar, por ejemplo, mediante el uso de los bits SETUID y SETGID en archivos).

### Derecho (Right)

Un **derecho (right)** es la **autorización** para efectuar ciertas operaciones sobre un objeto. Por ejemplo, en el par `(archivo A, {read, write})` dentro de un dominio, `read` y `write` son derechos que permiten a un proceso que se ejecuta dentro de ese dominio leer y escribir en el archivo A.

## 3. Defina POLA (Principle of least authority)

El **Principio de Mínima Autoridad** o **POLA (Principle of Least Authority)** establece que los procesos deben acceder **solo a los objetos que necesitan** y con los **derechos mínimos indispensables** para completar su tarea.

## 4. ¿Qué valores definen el dominio en UNIX?

En sistemas operativos como UNIX, el dominio de protección se define principalmente por la identidad del proceso o usuario que intenta acceder a los recursos. Específicamente, los valores que definen este dominio son:

- **UID (User ID)** y **GID (Group ID)**. En Unix, el dominio de protección para un proceso se determina por su ID de usuario (UID) y su ID de grupo (GID). Dos procesos que comparten el mismo UID y GID pueden acceder al mismo conjunto de archivos con los mismos permisos.

- **IDs de grupo suplementarios**. Además del ID de grupo efectivo, los permisos de acceso de un archivo también se deciden basándose en los IDs de grupo suplementarios del proceso.

- **Bits de modo de acceso (permisos UGO)**. El modo de archivo, almacenado en el campo `st_mode` de los atributos del archivo, contiene los bits de permiso de acceso. Estos bits controlan quién puede leer, escribir o ejecutar/buscar un archivo. Se categorizan para el **usuario** (propietario del archivo), el grupo (propietario del **grupo** del archivo) y **otros** usuarios.

- **Bits SETUID** y **SETGID**. Estos bits especiales pueden influir dinámicamente en el dominio de protección de un proceso. Por ejemplo, cuando se ejecuta un programa con el bit SETUID activado, el proceso adopta temporalmente el ID de usuario del propietario del archivo ejecutable, cambiando así su dominio de acceso para esa ejecución. El bit SETGID funciona de manera similar para el ID de grupo.

El sistema operativo decide los permisos de acceso para un archivo basándose en el UID y GID efectivos del proceso, sus IDs de grupo suplementarios, junto con el propietario del archivo, el grupo y los bits de permiso.

## 5. ¿Qué es ASLR (Address Space Layout Randomization)? ¿Linux provee ASLR para los procesos de usuario? ¿Y para el kernel?

El **ASLR (Address Space Layout Randomization)**, o Randomización del Diseño del Espacio de Direcciones, es un mecanismo de seguridad que busca **dificultar los ataques de desbordamiento de búfer en la pila (stack buffer overflow)**. Este mecanismo logra esto al **randomizar la ubicación de las bibliotecas, la pila (stack), el heap, los datos (data) y el segmento de texto (text) en la memoria de un proceso en cada ejecución**. Esto significa que, si un atacante obtiene un puntero útil en una ejecución, este puntero ya no será válido en la siguiente ejecución del programa. Si bien no es infalible, el ASLR es una ayuda importante para la seguridad.

Linux sí provee ASLR para los procesos de usuario. Se puede verificar y configurar el estado del ASLR en un sistema Linux a través del archivo `/proc/sys/kernel/randomize_va_space`. Los valores posibles para este parámetro son:

- **`0`:** ASLR deshabilitado.
- **`1`**: Randomiza la pila, la página virtual de objetos dinámicos compartidos (VDSO) y la memoria compartida. Los datos se ubican al final del segmento de texto.
- **`2`**: Randomiza la pila, la página VDSO, la memoria compartida y los datos.

Además del ASLR para los procesos de usuario, Linux también provee **KASLR (Kernel ASLR)**. Este mecanismo extiende la randomización al propio kernel del sistema operativo, aumentando la dificultad para que posibles atacantes puedan explotar vulnerabilidades en el núcleo.

## 6. ¿Cómo se activa/desactiva ASRL para todos los procesos de usuario en Linux?

En Linux, el **ASLR (Address Space Layout Randomization)** para todos los procesos de usuario se activa o desactiva a través del archivo del sistema `/proc/sys/kernel/randomize_va_space`.

Puedes verificar el estado actual de ASLR ejecutando el siguiente comando: `cat /proc/sys/kernel/randomize_va_space`

Para **activar o desactivar ASLR**, debes escribir un valor específico en este archivo. Los valores posibles y sus efectos son:

- **`0`**: **ASLR está deshabilitado**. Esto significa que las ubicaciones de la pila, heap, datos, texto y bibliotecas en la memoria de un proceso no se randomizarán en cada ejecución.

- **`1`**: **ASLR está habilitado para la pila, la página virtual de objetos dinámicos compartidos (VDSO) y la memoria compartida**. Los datos se ubicarán al final del segmento de texto.

- **`2`**: **ASLR está habilitado para la pila, la página VDSO, la memoria compartida y los datos**. Este es el nivel más completo de randomización para procesos de usuario.

Para cambiar el valor (y, por lo tanto, el comportamiento del ASLR), necesitarás privilegios de superusuario. Por ejemplo, para habilitar la randomización completa (valor `2`), usarías un comando como: `echo 2 | sudo tee /proc/sys/kernel/randomize_va_space`

El ASLR es un mecanismo de seguridad diseñado para **dificultar los ataques de desbordamiento de búfer en la pila (stack buffer overflow)**, ya que randomiza la ubicación de componentes clave en la memoria del proceso en cada ejecución. Esto hace que un puntero obtenido en una ejecución no sea válido en la siguiente, complicando los ataques.

Si bien el ASLR es una medida de seguridad importante, ocasionalmente puede ser deshabilitado para experimentación o, en casos muy raros, porque algún software específico lo requiera.

# B - Ejercicio introductorio: Buffer Overflow Simple

## 5. Después de realizar la explotación, reflexiona sobre las siguientes preguntas

- ¿Por qué el uso de gets() es peligroso?

  El uso de la función gets() es muy peligroso. La principal razón de esto es que **no proporciona ninguna protección contra el desbordamiento del búfer (buffer overflow) de la cadena de destino**.

  - **Falta de verificación de límites**: La función `gets()` lee caracteres de la entrada estándar (`stdin`) hasta que encuentra un carácter de nueva línea o el fin del archivo. No tiene forma de saber ni de verificar el tamaño del búfer (`s`) que se le proporciona.

  - **Desbordamiento de búfer**: Si la entrada de caracteres es más larga que el espacio asignado para la cadena `s`, `gets()` continuará escribiendo más allá de los límites de ese búfer. Esto resulta en un `desbordamiento de búfer`.

  - **Corrupción de datos y ejecución de código malicioso**: Un desbordamiento de búfer puede sobreescribir datos en otras áreas de la memoria, como otras variables locales en la pila, o incluso la dirección de retorno de la función. Si un atacante logra sobreescribir la dirección de retorno con un puntero a código malicioso (`shellcode`), podría tomar el control del programa con los privilegios del proceso atacado. Esto se clasifica como una **explotación de errores en el código**.

  - `Problemas en el comportamiento del programa`: Incluso sin intención maliciosa, un desbordamiento puede llevar a errores impredecibles, fallos del programa (`crashes`), o un comportamiento incorrecto.

  - **Modificación de memoria de solo lectura**: Si la cadena que `gets()` intenta modificar es una constante o reside en una sección de memoria de solo lectura, el programa podría recibir una señal fatal por intentar escribir en memoria protegida.

  Debido a estos riesgos, la GNU C Library incluye `gets()` solo por compatibilidad. **Se recomienda siempre usar `fgets()`** o **`getline()` en su lugar**, ya que estas funciones permiten especificar un tamaño máximo de caracteres a leer, evitando así el desbordamiento del búfer. De hecho, el enlazador (linker) de GNU (`ld`) emitirá una advertencia cada vez que se utilice `gets()`.

## 6. ¿Cómo se puede prevenir este tipo de vulnerabilidad?

  Para prevenir las vulnerabilidades de desbordamiento de búfer (`buffer overflow`) y otras explotaciones de errores en el código, se pueden implementar una combinación de prácticas de codificación segura, mecanismos de protección a nivel de compilador, y características de seguridad a nivel de sistema operativo.

  A continuación, se detallan las principales estrategias de prevención extraídas de los recursos:

- **Prácticas de Codificación Segura**:

  - **Evitar funciones inseguras**: Es fundamental **evitar el uso de funciones que no realizan verificación de límites** al manejar cadenas o entradas. Funciones como `gets()`, `scanf()` (sin especificación de ancho máximo), y `strcpy()` son consideradas peligrosas porque no tienen mecanismos para saber o verificar el tamaño del búfer de destino, lo que puede llevar a un desbordamiento si la entrada es más larga que el espacio asignado.

  - **Usar alternativas seguras con verificación de límites**: En su lugar, se deben preferir funciones que permiten especificar un tamaño máximo de caracteres a leer, como `fgets()`, `fread()`, `memcpy()`, y `strncpy()`. Un programa bien escrito debe **reportar la entrada inválida con un mensaje de error comprensible**, en lugar de fallar o provocar un `crash`.
  
- **Protecciones en Tiempo de Compilación y Ejecución (Nivel Compilador)**:
  
  - **Fortificación (`_FORTIFY_SOURCE`)**: La biblioteca GNU C ofrece la macro `_FORTIFY_SOURCE` para el fortalecimiento de las llamadas a algunas de sus funciones. Al definir esta macro, se habilita código que v**alida las entradas pasadas a las funciones**. Si el compilador no puede garantizar la seguridad de las entradas, la llamada a la función puede ser reemplazada por una variante "endurecida" que realiza verificaciones de seguridad adicionales en tiempo de ejecución. Esto también activa diagnósticos en tiempo de compilación para detectar valores de retorno no verificados, promoviendo una mejor gestión de errores por parte del desarrollador. Si alguna de estas **verificaciones de seguridad falla en tiempo de ejecución**, el programa se terminará con una señal `SIGABRT`. Hay diferentes niveles de fortificación (`1`, `2`, `3`) que ofrecen distintos grados de protección y rendimiento.

- **Mecanismos de Protección de Memoria (Nivel Kernel)**:

  - **ASLR (Address Space Layout Randomization)**: Este mecanismo de seguridad **dificulta los ataques de desbordamiento de búfer en la pila** (`stack buffer overflow`). Lo logra al **randomizar la ubicación de componentes clave** como las bibliotecas, la pila (`stack`), el heap, los datos (`data`) y el segmento de texto (`text`) en la memoria de un proceso en cada ejecución. Esto significa que, si un atacante obtiene un puntero útil en una ejecución, este puntero ya no será válido en la siguiente, complicando la explotación. ASLR no es infalible, pero es una ayuda importante para la seguridad.

  - **KASLR (Kernel ASLR)**: Extiende el principio de ASLR al propio kernel del sistema operativo, **randomizando la ubicación del kernel en memoria**. Esto aumenta la dificultad para los atacantes que intentan explotar vulnerabilidades en el núcleo del sistema.

  - **NX (No eXecute) bit**: Este es un bit a nivel de hardware que permite **marcar áreas de memoria como no ejecutables**. Su propósito es prevenir la ejecución de código desde regiones de memoria que solo deberían contener datos, lo que mitiga los ataques de inyección de código.

- **Aislamiento de Procesos y Limitación de Recursos (Nivel Sistema)**:

  - **Namespaces**: Permiten **aislar lo que los procesos ven** del resto del sistema. Las modificaciones a un recurso dentro de un `namespace` quedan contenidas en ese `namespace`. Linux provee `namespaces` para varios recursos, como la comunicación entre procesos (IPC), la red, los puntos de montaje, los IDs de procesos (PID), los IDs de usuario y grupo, el nombre del host (UTS) y el directorio raíz de cgroup.

  - **CGroups (Control Groups)**: Son una característica del kernel de Linux que permite **organizar procesos en grupos jerárquicos y limitar y monitorear el uso de varios tipos de recursos**, incluyendo tiempo de CPU, memoria y ancho de banda de E/S. También se pueden usar para priorizar recursos.
  
  - **SystemD**: Este sistema de inicialización puede **restringir los privilegios de un servicio** utilizando una combinación de técnicas como `CGroups`, `Namespaces`, `ulimit`, `Capabilities` (para limitar privilegios de procesos con UID=0), filtrado de llamadas al sistema, y ejecución en un entorno `CHROOT`. Además, puede proteger directorios sensibles como `/proc`, `/tmp` y `/home`.
  
  - **AppArmor**: Es una implementación del Módulo de Seguridad de Linux (LSM) que se centra en el **confinamiento de privilegios de los procesos**. Permite a los administradores definir perfiles de seguridad que especifican a qué archivos puede acceder un programa y qué operaciones puede realizar. Los perfiles de AppArmor pueden operar en modo de aplicación estricta (**`enforcing`**), que bloquea acciones no autorizadas, o en modo de **registro (`complain`)**, que solo registra las acciones no autorizadas sin bloquearlas, lo que facilita el ajuste de los perfiles. AppArmor complementa los permisos de acceso tradicionales.

# C - Ejercicio: Buffer Overflow reemplazando dirección de retorno

## 11. Conteste
  
### ¿Qué efecto tiene setear el bit setuid en un programa si el propietario del archivo es root? ¿Qué efecto tiene si el usuario es por ejemplo nobody?

  El bit `setuid` (set-user-ID) es un mecanismo de seguridad en Linux que permite que un programa ejecutable se ejecute con los privilegios del propietario del archivo, en lugar de los privilegios del usuario que lo ejecuta. Esto se logra estableciendo un **ID de usuario del archivo** (`file user ID`) para el proceso, que es el ID de usuario del propietario del archivo ejecutable. Luego, el sistema cambia el **ID de usuario efectivo** (`effective user ID`) del proceso a este `file user ID`, mientras que el **ID de usuario real** (`real user ID`) permanece como el del usuario que ejecutó el programa.

  Cuando el bit `setuid` se establece en un programa cuyo propietario es `root` (UID 0), ocurre lo siguiente:

- **ID de usuario efectivo (`effective user ID`)**: Al ejecutar el programa, el ID de usuario efectivo del proceso se convierte en `0` (root). Un proceso con un ID de usuario efectivo de cero es un **proceso privilegiado**, lo que le otorga la capacidad de realizar muchas operaciones que están restringidas para usuarios no privilegiados, como acceder directamente al hardware o a cualquier ubicación de memoria.

- **ID de usuario real (`real user ID`)**: El ID de usuario real del proceso sigue siendo el del usuario que lo ejecutó. Esto es crucial para la seguridad, ya que programas como `access()` pueden usar el ID real para verificar permisos, asegurando que el programa no realice acciones que el usuario original no debería poder hacer.

- **Propósito**: Este es el mecanismo utilizado por programas como `passwd` (para cambiar contraseñas) o `sudo`, que necesitan privilegios de `root` para realizar ciertas tareas, pero son ejecutados por usuarios comunes.

**Implicaciones de seguridad (cuando el propietario es `root`):**

- Los programas con `setuid root` son **fuentes potenciales de vulnerabilidades** si no se codifican con extremo cuidado. Un atacante podría explotar un error en el programa para ejecutar código malicioso con privilegios de `root`.

- Se recomienda encarecidamente que estos programas **reduzcan sus privilegios** (`seteuid` o `setreuid`) cuando no los necesitan activamente, volviendo al ID de usuario real, y solo los recuperen para operaciones específicas.

- Además, se debe **evitar el uso de funciones que invocan una shell** (como `system()`) en programas `setuid`, ya que esto puede permitir la ejecución de comandos arbitrarios con privilegios elevados.

Cuando el bit setuid se establece en un programa cuyo propietario es un usuario no `root` (como `nobody`), el efecto es similar, pero con un alcance de privilegios diferente:

- **ID de usuario efectivo (`effective user ID`)**: Al ejecutar el programa, el ID de usuario efectivo del proceso se convierte en el UID del usuario `nobody`.

- **ID de usuario real (`real user ID`)**: El ID de usuario real del proceso sigue siendo el del usuario que lo ejecutó.

- **Propósito**: Esta configuración se utiliza para permitir que el programa acceda a recursos específicos que son propiedad del usuario `nobody`, sin otorgarle los privilegios completos de `root`. Por ejemplo, un programa de juego podría tener un archivo de puntuaciones que solo el usuario `games` (un usuario creado para ese fin) puede escribir. Al configurar el programa con `setuid games`, cualquier usuario que ejecute el juego permitirá que el juego actualice el archivo de puntuaciones, mientras que los usuarios no pueden modificarlo directamente.

**Implicaciones de seguridad (cuando el propietario es un usuario no root):**

- Aunque es inherentemente menos peligroso que `setuid root`, sigue siendo una elevación de privilegios y debe manejarse con cuidado.

- El programa puede alternar entre los privilegios del usuario `nobody` y los del usuario real que lo ejecuta utilizando funciones como `seteuid()` o `setreuid()`, especialmente si el sistema soporta la característica `_POSIX_SAVED_IDS` (conocida como IDs guardados en POSIX).

- De manera similar al caso de `root`, se deben seguir las prácticas de codificación segura para evitar que el programa realice acciones no deseadas o sea explotado para obtener privilegios que el usuario `nobody` no debería tener, o para modificar archivos a los que el usuario original no tenía acceso.

### Compare el resultado del siguiente comando con la dirección de memoria de `privileged_fn()`. ¿Qué puede notar respecto a los octetos? ¿A qué se debe esto?

  ```python payload_pointer.py --padding <padding> --pointer <pointer> | hd```

### ¿Cómo ASLR ayuda a evitar este tipo de ataques en un escenario real donde el programa no imprime en pantalla el puntero de la función objetivo?

**ASLR (Address Space Layout Randomization)** ayuda a evitar ataques de desbordamiento de búfer (`buffer overflow`) en escenarios reales donde el programa no imprime directamente los punteros de las funciones objetivo, al **dificultar la predicción de ubicaciones de memoria**.

**Cómo funciona:**
  
- **Naturaleza de los ataques de desbordamiento de búfer**: Un ataque de desbordamiento de búfer busca sobrescribir intencionalmente datos en ciertas zonas de memoria. Si se sobrescribe una posición de memoria con un valor sensible, se podría encadenar la ejecución de un programa malicioso, como un *shellcode*. Para que un atacante logre esto, necesita saber la dirección exacta en la memoria a la que debe saltar para ejecutar su código inyectado o una función legítima ya existente en el programa (como una función del sistema).

- **Mecanismo de ASLR**: ASLR es una protección provista por el kernel que **randomiza la ubicación de componentes clave del espacio de memoria** de un proceso en cada ejecución. Esto incluye:

  - La **pila (`stack`)** (donde se almacenan variables locales y, crucialmente para los desbordamientos, la dirección de retorno de las funciones).
  - El **heap** (donde se asignan variables dinámicamente).
  - La sección de **datos (`data)`**.
  - La sección de **texto (`text`)** (donde están las instrucciones del programa).
  - Las **bibliotecas** cargadas por el programa.

- **Cómo ASLR dificulta el ataque sin imprimir punteros**: En un escenario donde el programa no imprime directamente los punteros de las funciones, el atacante ya enfrenta la dificultad de no conocer estas direcciones. ASLR **agrava esta dificultad** al asegurar que, incluso si un atacante logra determinar un puntero útil en una ejecución específica (por ejemplo, a través de alguna otra fuga de información que no sea una impresión directa), **ese puntero ya no será válido en la siguiente ejecución**.

  - Esto significa que la dirección de la pila, del *heap*, de las variables globales o de las funciones de las bibliotecas del sistema, cambia con cada inicio del programa.

  - Sin esta predicción de direcciones, es extremadamente difícil para el atacante sobrescribir la dirección de retorno de una función en la pila (o cualquier otro puntero) con una dirección válida que les permita controlar el flujo de ejecución del programa. El atacante no puede "apuntar" a su código inyectado o a una función ROP (Return-Oriented Programming) conocida, porque su ubicación es aleatoria.

En resumen, aunque el programa no imprima explícitamente los punteros, la naturaleza aleatoria de ASLR hace que los atacantes no puedan predecir las direcciones de memoria necesarias para un ataque exitoso de desbordamiento de búfer. Esto **no es infalible, pero es una ayuda significativa** para la seguridad.

### ¿Cómo podría evitar este tipo de ataques en un módulo del kernel de Linux? ¿Qué mecanismo debería estar habilitado?

Para evitar ataques de desbordamiento de búfer **(buffer overflow**) en un módulo del kernel de Linux, es fundamental comprender que, a diferencia de los programas de espacio de usuario, los módulos del kernel **se ejecutan en modo privilegiado (modo Kernel)**. Esto significa que un error en un módulo, como un desbordamiento de búfer, puede tener consecuencias mucho más graves, llegando a provocar un **Kernel Panic** (bloqueo total del sistema operativo) o permitir a un atacante obtener **privilegios a nivel de kernel**, lo que se considera un compromiso total del sistema ("game over").

Los mecanismos que deberían estar habilitados y las prácticas que se deben seguir para prevenir este tipo de ataques en un módulo del kernel son:

- **KASLR (Kernel Address Space Layout Randomization)**: Este es el equivalente a ASLR para el kernel. Su función es **randomizar la ubicación en memoria** de las imágenes del kernel, las bibliotecas, la pila y el *heap* del kernel en cada arranque del sistema. Al hacer que las direcciones de memoria sean impredecibles, KASLR **dificulta enormemente que un atacante pueda predecir las direcciones exactas** a las que debe saltar para ejecutar código malicioso o sobrescribir estructuras de datos sensibles, incluso si logra provocar un desbordamiento de búfer.

- **NX (No eXecute) bit**: Este es un **bit de hardware** que permite marcar áreas de memoria como **no ejecutables**. Si un atacante logra inyectar código malicioso (conocido como *shellcode*) en una región de datos (como la pila o el *heap*) a través de un desbordamiento de búfer, el bit NX evitará que ese código se ejecute, ya que la memoria está designada solo para datos y no para instrucciones.

- **Uso estricto de APIs seguras para la transferencia de datos entre espacio de usuario y kernel**:

  - Una de las principales vías para los desbordamientos de búfer en módulos del kernel es cuando estos procesan **datos provenientes del espacio de usuario**. Los punteros proporcionados por el usuario no pueden considerarse válidos y **no deben apuntar al espacio del kernel**.

  - Para evitar que un atacante manipule el flujo de ejecución o sobrescriba datos sensibles del kernel a través de datos de usuario, los módulos del kernel **deben utilizar funciones seguras** como `copy_from_user()` y `copy_to_user()` para transferir información entre el espacio de usuario y el kernel.

  - Estas funciones **realizan verificaciones de límites** (validando que el área de memoria de usuario es accesible y que la transferencia no excede los límites definidos). Si un módulo usara `memcpy()` o funciones similares directamente sobre un búfer proporcionado por el usuario sin estas validaciones, sería vulnerable a un desbordamiento.

Es importante señalar que, aunque ASLR y NX bit son protecciones a nivel de sistema que el kernel habilita, la **correcta implementación del módulo** es crucial. Los programas privilegiados, como los módulos del kernel, deben ser codificados con extremo cuidado, validando todas las entradas y evitando funciones inseguras o la invocación de *shells*.

Por otro lado, la función `memfd_secret()`, mencionada en nuestra conversación, no está diseñada para prevenir directamente desbordamientos de búfer en módulos del kernel. Su propósito es permitir que un proceso de espacio de usuario cree áreas de memoria que sean inaccesibles para el kernel. Aunque protege contra "errores de aplicación (por ejemplo, usar accidentalmente el área `secretmem` como búfer de envío) y errores de kernel/CPU", y también ayuda contra "lecturas de búfer simples... sin ejecución de código arbitrario", explícitamente se indica que **no protege contra exploits del kernel o acceso de *root***. Si un atacante ya tiene derechos de ejecución de código arbitrario a nivel de kernel (lo que un desbordamiento de búfer en un módulo podría facilitar), `memfd_secret()` no sería una barrera, ya que en ese punto, "se acabó el juego".

# D - Ejercicio SystemD

## investigue los comandos

- **a. `systemctl enable [unit]`**
  
  Habilita un servicio para que se inicie automáticamente en el arranque del sistema.

- **b. `systemctl disable [unit]`**
  
  Deshabilita un servicio para que no se inicie automáticamente en el arranque.

- **c. `systemctl daemon-reload`**

  Recarga la configuración de `systemd`, aplicando cambios hechos en los archivos de los servicios.

- **d. `systemctl start [unit]`**

  Inicia (activa) un servicio inmediatamente.

- **e. `systemctl stop [unit]`**

  Detiene (desactiva) un servicio inmediatamente.

- **f. `systemctl status [init]`**
  
  Muestra el estado actual de un servicio, incluyendo si está activa y sus últimos registros.

- **g. `systemd-cgls`**

  Muestra la jerarquía de los grupos de control (cgroups) del sistema en forma de árbol.

- **h. `journalctl -u [unit]`**

  Muestra los registros (logs) del journal que han sido generados por un servicio específico.

## investigue las siguientes opciones que se pueden configurar en una unit service de systemd

# Opciones de configuración en una unit service de systemd

A continuación se describen brevemente distintas opciones que se pueden utilizar en archivos de unidad (`.service`) de systemd, todas en una sola línea cada una:

- **a. IPAddressDeny e IPAddressAllow**  
  Restringen el acceso de red del servicio a direcciones IP específicas.  
  
  `IPAddressDeny=0.0.0.0/0` bloquea todo acceso, mientras que `IPAddressAllow=192.168.1.0/24` permite solo ese rango.  
  
  Se pueden usar múltiples líneas o combinarlas con listas separadas por espacios.  
  
  Ejemplo:  
  `IPAddressDeny=0.0.0.0/0 ::/0`  
  `IPAddressAllow=192.168.1.100 10.0.0.0/8`

- **b. User y Group**  
  Ejecutan el servicio con un usuario y grupo específicos.  
  
  Ejemplo:  
  `User=www-data`  
  `Group=www-data`

- **c. ProtectHome**  
  Restringe el acceso del servicio a los directorios de los usuarios (`/home`, `/root`, `/run/user`).  

  Valores posibles: `yes`, `read-only`, `tmpfs`  
  
  Ejemplo:  
  `ProtectHome=yes`

- **d. PrivateTmp**  
  Asigna al servicio un espacio temporal privado, aislado del sistema principal.  

  Evita que el servicio acceda a `/tmp` y `/var/tmp` del sistema.  

  Ejemplo:  
  `PrivateTmp=yes`

- **e. ProtectProc**  
  Restringe el acceso del servicio al sistema de procesos (`/proc`).  

  Valores posibles: `default`, `invisible`, `ptraceable`  

  Ejemplo:  
  `ProtectProc=invisible`

- **f. MemoryAccounting, MemoryHigh y MemoryMax**  
  Controlan y limitan el uso de memoria del servicio.  
  `MemoryAccounting=yes` habilita el seguimiento de uso de memoria.

  `MemoryHigh` define un límite flexible, `MemoryMax` uno estricto.  

  Ejemplo:  
  `MemoryAccounting=yes`  
  `MemoryHigh=500M`  
  `MemoryMax=1G`
