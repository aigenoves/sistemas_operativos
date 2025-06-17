# Resumen: Protección y Seguridad en Sistemas Operativos

Este resumen cubre los conceptos fundamentales de protección y seguridad en sistemas operativos, sus mecanismos clave y su aplicación en entornos como Linux.

## Introducción a Protección y Seguridad

Los sistemas operativos son esenciales para **proteger los recursos informáticos** (como datos, CPU, memoria y dispositivos de E/S) contra accesos no autorizados, destrucción intencional o inconsistencias accidentales. La manera en que se implementan estos mecanismos define el **nivel de seguridad** de un sistema.

Es importante diferenciar entre:

- **Protección**: Se refiere a los **mecanismos específicos** que el sistema operativo implementa para resguardar información y controlar el acceso de procesos o usuarios a los recursos. Es el "cómo" se logra el control.
- **Seguridad**: Es un **concepto más amplio** que mide la confianza en la preservación de la integridad del sistema y sus datos. Abarca la defensa contra **amenazas** (confidencialidad, integridad, disponibilidad) e intrusos, además de la prevención de la pérdida accidental de información. Es el "qué" se busca lograr.

El control de acceso se divide en **acceso al sistema** (mediante autenticación y bases de datos de usuarios) y **acceso a recursos dentro del sistema** (a través de permisos y control de acceso obligatorio o no).

Una distinción fundamental es entre **políticas** (qué se quiere lograr) y **mecanismos** (cómo se implementa una política), lo que permite flexibilidad en el sistema.

## Dominios de Protección

Un **dominio de protección** es un conjunto de pares que asocia un objeto con los derechos de acceso permitidos sobre él. Este concepto especifica qué recursos puede acceder un proceso y qué operaciones puede realizar.

- Un dominio puede estar ligado a un **usuario**, un **proceso** o un **procedimiento**.
- En sistemas Unix, el dominio se define por el **UID** (User ID) y el **GID** (Group ID). Procesos con el mismo UID/GID comparten el mismo dominio.
- Los procesos pueden **cambiar de dominio**, por ejemplo, utilizando los bits **SETUID** y **SETGID** en Unix sobre los archivos.

## Matriz de Acceso

La matriz de acceso es un modelo formal que representa las políticas de protección. Sus filas representan dominios y sus columnas representan objetos. Cada celda de la matriz indica las operaciones (derechos) que un proceso puede realizar sobre un objeto dentro de un dominio específico.

Las operaciones que pueden modificar esta matriz incluyen:

- **Switch**: Permite a un proceso cambiar entre dominios.
- **Copy/Limited Copy**: Permite transferir derechos entre dominios.
- **Owner**: Si un dominio posee el derecho "owner" sobre un objeto, puede añadir o eliminar cualquier entrada en la columna de ese objeto.
- **Control**: Si un dominio posee el derecho "control" sobre otro dominio, puede eliminar cualquier derecho de acceso dentro de la fila de ese dominio.

### Implementación de la Matriz de Acceso

Debido a la ineficiencia de almacenar la matriz directamente (muchas celdas vacías), se emplean métodos optimizados:

- **Tabla Global**: Una lista simple de tuplas (dominio, objeto, derechos de acceso).
- **Listas de Control de Acceso (ACL)**: Cada **objeto** tiene una lista de pares (dominio, derechos) asociados, permitiendo un control de acceso granular. En Linux, los **permisos de archivo UGO** y las **ACL extendidas** son ejemplos.
- **Listas de Capacidades**: Una lista de (objeto, derechos) asociada con cada **dominio**. Esta lista está protegida y solo es accesible por el sistema operativo. Presenta desafíos para revocar o modificar permisos, ya que requiere recorrer todas las listas.

## Sistemas Confiables y Explotación de Código

La seguridad de un sistema también depende de la confiabilidad del código. El **código malintencionado** (como virus, gusanos, troyanos) y los **errores de programación** (como backdoors) son amenazas significativas. Construir sistemas seguros es complejo, ya que la **adición de funcionalidad** a menudo introduce nuevas vulnerabilidades. Los sistemas minimalistas tienden a ser más seguros pero menos funcionales.

Los atacantes explotan errores en el código del sistema operativo o en procesos con altos privilegios para alterar su funcionamiento normal. Algunas técnicas comunes incluyen:

- **Desbordamiento de búfer (Buffer Overflow)**: Consiste en sobrescribir datos en la memoria de forma intencionada. Si se sobrescribe con un valor sensible o **shellcode** (código malicioso diseñado para obtener privilegios), puede conducir a la ejecución de código arbitrario y a la **escalada de privilegios**. Funciones inseguras como `gets()`, `scanf()` y `strcpy()` son propensas a esto.

Para mitigar estos ataques, existen protecciones del kernel de Linux:

- **ASLR (Address Space Layout Randomization)**: Randomiza las direcciones de memoria del stack, heap, data y bibliotecas en cada ejecución del proceso, dificultando la predicción de las ubicaciones del código malicioso.
- **NX (No eXecute) bit**: Marca áreas de memoria como no ejecutables, impidiendo la ejecución de código desde regiones de datos como el stack.

## Restricciones de Privilegios

Varias herramientas y mecanismos se usan para confinar los privilegios de los procesos:

- **SystemD**: Puede restringir los privilegios de un servicio mediante la combinación de:
  - CGroups (para limitar el uso de recursos).
  - Namespaces (para aislar procesos).
  - Ulimit.
  - Capabilities (para limitar privilegios de UID 0).
  - System call filtering (filtrado de llamadas al sistema).
  - Ejecución en un **CHROOT**.
  - Protección de directorios como `/proc`, `/tmp`, `/home`.
  - Filtrado de IPs.
  - DynamicUser.

- **AppArmor**: Es un Módulo de Seguridad de Linux (LSM) que confina los privilegios de los procesos a través de perfiles de seguridad basados en las rutas de los ejecutables. Puede operar en modo **`enforcing`** (bloquea acciones no autorizadas) o **`complain`** (solo las registra). Es más simple de configurar que SELinux. Algunas herramientas para interactuar con **AppArmor** son aa-enabled, aa-status, `aa-complain <path-programa>`, `aa-enforce <path-programa>`, `aa-genprof <path-programa>`, `apparmor_parse -r <path-profile>`, y el monitoreo con `journal -xf` o `tail -f /var/log/audit/audit.log`.

- **memfd_secret()**: Introducida en Linux 5.14, es una llamada al sistema que permite a un proceso de usuario crear una región de memoria inaccesible para cualquier otro proceso, incluyendo el kernel. Esta memoria es ideal para almacenar datos criptográficos sensibles. Como medida de seguridad, desactiva la hibernación del sistema si está activa, para prevenir que los datos se escriban en almacenamiento persistente.

Otros mecanismos de seguridad importantes incluyen el **cifrado de disco** y los **firewalls** (como iptables y firewalld) para configurar reglas de filtrado de paquetes.
