# Resumen de Seguridad en Sistemas Operativos

Este documento resume los conceptos clave sobre protección y seguridad en sistemas operativos, basándose en la documentación de la Facultad de Informática de la UNLP.

## 1. Conceptos Fundamentales

### Protección vs. Seguridad

* **Protección**: Se refiere a los mecanismos específicos implementados por el sistema operativo para controlar el acceso de procesos o usuarios a los recursos del sistema.
* **Seguridad**: Es un concepto más amplio que mide la confianza en la capacidad de un sistema para preservar su integridad y la de sus datos frente a amenazas, intrusos o pérdidas accidentales. El nivel de seguridad se determina por la correcta aplicación de los mecanismos de protección.

### Políticas y Mecanismos

* **Políticas (Qué)**: Definen los objetivos de seguridad y lo que se desea hacer, sin entrar en detalles de implementación.
* **Mecanismos (Cómo)**: Son las implementaciones y configuraciones concretas que se utilizan para cumplir con las políticas establecidas.
* Esta separación entre políticas y mecanismos garantiza la flexibilidad del sistema.

### Dominios de Protección

* Un sistema informático es una colección de **procesos** y **objetos** (hardware o software).
* Un **dominio** es un conjunto de pares `(objeto, derecho)`, donde cada par especifica un objeto y las operaciones permitidas sobre él. Un derecho es la autorización para efectuar una operación.
* El **Principio de Mínima Autoridad (POLA)** establece que un proceso solo debe acceder a los objetos que necesita para completar su tarea, con los mínimos derechos necesarios.
* La relación entre un proceso y un dominio puede ser **estática** (privilegios fijos) o **dinámica** (los privilegios pueden cambiar, como con `setuid` en Unix).

## 2. Matriz de Acceso

La Matriz de Acceso es un modelo abstracto que implementa las políticas de seguridad de un sistema.

* **Estructura**: Las filas representan dominios y las columnas representan objetos. La celda `access(i, j)` contiene el conjunto de derechos que un proceso en el dominio `Di` tiene sobre el objeto `Oj`.

### Operaciones Especiales sobre la Matriz

Existen operaciones especiales para gestionar los derechos dentro de la matriz:

* **`switch`**: Permite a un proceso cambiar de un dominio a otro si tiene el derecho `switch` sobre el dominio destino.
* **`owner`**: El derecho `owner` sobre un objeto (columna) permite a un proceso en un dominio agregar o borrar derechos para ese objeto a cualquier otro dominio.
* **`copy`**: Permite a un proceso copiar un derecho de acceso sobre un objeto a otro dominio en la misma columna.
* **`control`**: Aplicable solo a objetos de dominio, permite a un proceso en el dominio `Di` remover derechos de la fila del dominio `Dj` si tiene el derecho `control` en `access(i, j)`.

### Implementaciones de la Matriz

Debido a que la matriz suele ser dispersa, su implementación se optimiza:

* **Tabla Global**: Una única tabla con tuplas `<dominio, objeto, derechos>`. Es simple pero puede ser muy grande.
* **Listas de Control de Acceso (ACL)**: Asociada a cada objeto (columna de la matriz), contiene una lista de los dominios que pueden acceder a él y con qué derechos.
* **Listas de Capacidades**: Asociada a cada dominio (fila de la matriz), contiene una lista de objetos y las operaciones que el dominio puede realizar sobre ellos.

## 3. Amenazas y Explotación de Errores

* **Código malicioso**: Los atacantes aprovechan errores de codificación en el SO o en procesos con privilegios para alterar su funcionamiento.
* **Desbordamiento de Búfer (Buffer Overflow)**: Es un error de software que ocurre al intentar copiar una cantidad de datos mayor a la que un búfer puede almacenar.
  * **Objetivo**: El ataque busca sobrescribir áreas de memoria adyacentes al búfer, como la dirección de retorno de una función en la pila.
  * **Técnica**: Se utilizan funciones inseguras como `gets()` o `strcpy()` que no verifican los límites de los datos de entrada. Al sobrescribir la dirección de retorno, el atacante puede redirigir la ejecución a un código malicioso propio (shellcode).

## 4. Mecanismos de Seguridad en Linux

Linux implementa una variedad de mecanismos para proteger el sistema.

### Control de Acceso: DAC vs. MAC

* **DAC (Control de Acceso Discrecional)**: El propietario de un recurso decide quién puede acceder a él. Es el modelo tradicional de permisos de Unix (`UGO`).
* **MAC (Control de Acceso Obligatorio)**: El acceso se gestiona mediante políticas de seguridad a nivel de todo el sistema que los usuarios no pueden modificar. AppArmor y SELinux son ejemplos de MAC. MAC actúa como una capa de verificación adicional después de DAC.

### Mecanismos Principales

* **Permisos de Filesystem**: Incluye los permisos `UGO` (Usuario, Grupo, Otros) y las **ACLs** extendidas.
* **Namespaces**: Permiten el aislamiento de los recursos globales del sistema para grupos de procesos.
* **CGroups**: Permiten limitar y contabilizar el uso de recursos como CPU, memoria, I/O, etc..
* **Capabilities**: Dividen los privilegios de `root` en unidades más pequeñas y asignables, permitiendo que los procesos tengan solo los privilegios que necesitan sin ser `UID=0`.

### Protecciones a Nivel de Kernel

* **ASLR (Address Space Layout Randomization)**: Aleatoriza las direcciones de memoria de la pila, el heap y las bibliotecas en cada ejecución de un proceso. Esto dificulta los ataques de buffer overflow, ya que el atacante no conoce de antemano las direcciones de memoria a las que saltar. Se puede configurar mediante `/proc/sys/kernel/randomize_va_space`.
* **NX (No-Execute) Bit**: Permite marcar páginas de memoria como no ejecutables, evitando que un atacante pueda inyectar y ejecutar código en áreas de datos como la pila o el heap.

### AppArmor

* **Descripción**: Es una implementación de Linux Security Module (LSM) que confina programas a un conjunto limitado de recursos. Utiliza perfiles de seguridad basados en las rutas de los archivos ejecutables.
* **Modos de Operación**:
  * **`enforcing`**: Aplica las reglas del perfil y bloquea cualquier acción no autorizada.
  * **`complain`**: No bloquea las acciones no permitidas, pero las registra. Es útil para desarrollar y depurar perfiles.
* **Herramientas Comunes**:
  * `aa-status`: Muestra el estado actual de AppArmor y los perfiles cargados.
  * `aa-genprof <ejecutable>`: Asistente que ayuda a generar un nuevo perfil para un programa.
  * `aa-enforce <perfil>`: Pone un perfil en modo `enforcing`.
  * `aa-complain <perfil>`: Pone un perfil en modo `complain`.
