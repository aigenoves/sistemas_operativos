# Resumen de Virtualización

La virtualización es una **técnica que permite realizar la abstracción de los recursos de una computadora**, como redes, almacenamiento y centros de datos. Actúa como una **capa abstracta que desacopla el hardware físico del sistema**, ocultando detalles técnicos mediante la encapsulación. Esto permite que **una computadora pueda realizar el trabajo de varias**, compartiendo los recursos de un único dispositivo de hardware.

## Tipos de Virtualización

Existen diferentes niveles de virtualización:

* **Nivel de Proceso**: Permite la portabilidad entre distintos sistemas. Un ejemplo es la **Java Virtual Machine (JVM)**.
* **Nivel de Almacenamiento**: Presenta una vista lógica del almacenamiento al usuario, quien no necesita saber dónde están guardados los datos (ejemplos: RAID, LVM).
* **Nivel de Red**: Integra recursos de hardware de red con recursos de software.
* **Nivel de Sistema Operativo (Contenedores)**: Permite la existencia de **múltiples instancias de espacio de usuario aisladas** dentro de un único sistema operativo.
* **Nivel de Sistema (Máquinas Virtuales)**: Permite la creación de máquinas virtuales completas.

## ¿Por Qué Virtualizar? (Motivos y Ventajas)

La virtualización ofrece numerosas ventajas:

* **Alta utilización del hardware**.
* Capacidad de **ejecutar aplicaciones heredadas (legacy)** que no son compatibles con hardware o sistemas operativos modernos.
* **Prueba de aplicaciones no seguras** en entornos aislados.
* Creación de entornos de ejecución con **recursos limitados o simulados**.
* **Simulación de redes de computadoras** independientes.
* Ejecución simultánea de **múltiples sistemas operativos diferentes**.
* Facilita el **testeo y monitoreo de rendimiento**.
* Permite la **ejecución de sistemas operativos existentes en ambientes multiprocesador** que comparten memoria.
* **Facilidad de migración** y ahorro de energía ("green IT").
* Las máquinas virtuales se **encapsulan en archivos dentro de un sistema de archivos**, lo que las hace fáciles de almacenar, copiar, hacer copias de seguridad, restaurar, expandir y mover rápidamente entre servidores.

## Ideas Generales sobre Virtualización

Es una **capa de abstracción sobre el hardware** para optimizar la utilización de recursos y aumentar la flexibilidad. Permite que **múltiples máquinas virtuales (VMs) o entornos virtuales (EVs)**, con sistemas operativos idénticos o distintos, se ejecuten de manera aislada. Cada VM dispone de su propio hardware virtual (RAM, CPU, NIC, etc.), y el sistema operativo invitado (guest) percibe un conjunto consistente de hardware, sin necesariamente ver el hardware real.

## Componentes Clave

* **Software Anfitrión (Host)**: Es el software que realiza la simulación.
* **Software Invitado (Guest)**: Es lo que se desea simular, que puede ser un sistema operativo completo.
* **Monitor de Máquinas Virtuales (VMM) o Hipervisor**: Es el programa que se ejecuta sobre el hardware para implementar las máquinas virtuales.
  * Se encarga de **controlar los recursos y la planificación de los invitados**.
  * Necesita ejecutarse en **modo supervisor (privilegiado)**.
  * El software invitado se ejecuta en **modo usuario (no privilegiado)**.
  * Las **instrucciones privilegiadas** ejecutadas por los invitados generan **traps (interrupciones)** al VMM, que las interpreta o emula.

## Características de una Máquina Virtual (según Popek y Goldberg, 1974)

* **Equivalencia / Fidelidad**: Un programa ejecutándose en un VMM debería comportarse idénticamente a como lo haría directamente sobre el hardware subyacente.
* **Control de recursos / Seguridad**: El VMM debe controlar completamente los recursos virtualizados que proporciona a cada invitado.
* **Eficiencia / Rendimiento**: Una fracción estadísticamente dominante de las instrucciones deben ejecutarse directamente por el hardware, sin intervención del VMM.

## Instrucciones Privilegiadas y Sensibles

* **Instrucciones Privilegiadas**: Provocan una interrupción cuando se ejecutan en modo usuario.
* **Instrucciones Sensibles**: Deben ejecutarse en modo kernel (control de I/O, configuración de hardware, administración de interrupciones).
* Para que un sistema soporte virtualización, las instrucciones sensibles deben ser un subconjunto de las privilegiadas. La arquitectura **x86 no cumple completamente con este teorema** debido a que no todas las instrucciones sensibles son privilegiadas.

## Anillos de Privilegios en x86

Los procesadores x86 utilizan un sistema de **anillos (rings)** para la protección de privilegios:

* **Ring 0**: Nivel más privilegiado (modo Kernel).
* **Ring 1, 2, 3**: Niveles menos privilegiados (modo usuario, típicamente Ring 3).
El objetivo es que los hipervisores se ejecuten en Ring 0 y los sistemas operativos invitados en un anillo de menor privilegio.

## Tipos de Hipervisores

* **Hipervisor Tipo 1 (Bare-metal / Nativo)**:
  * Se ejecuta **directamente sobre el hardware**.
  * El hipervisor se ejecuta en modo kernel real, y cada VM opera como un proceso en un "modo kernel virtual" (que es en realidad modo usuario).
  * El sistema operativo invitado **no requiere modificaciones**.
  * Necesita **asistencia de hardware** para funcionar eficientemente.
  * Ejemplos: **KVM, VMWare ESXi, Xen**.
* **Hipervisor Tipo 2 (Hosted)**:
  * Se ejecuta como un **programa de usuario sobre un sistema operativo anfitrión (host OS)**.
  * Emula el hardware para el sistema operativo invitado.
  * El host OS es quien se comunica directamente con el hardware.
  * Ejemplos: **VirtualBox, VMWare Workstation**.

## Técnicas de Virtualización

* **Emulación**:
  * Provee la **funcionalidad completa de un procesador deseado mediante software**.
  * Permite emular un tipo de procesador sobre otro (ejemplo: **QEMU**).
  * Todas las instrucciones son capturadas, interpretadas y traducidas, lo que tiende a ser **lento**.
* **Virtualización Completa (Full Virtualization)**:
  * Particiona un procesador físico en distintos contextos, donde cada uno corre sobre el mismo procesador.
  * No requiere modificar el sistema operativo invitado.
  * Utiliza **traducción binaria** (introducida por VMWare para x86) para traducir dinámicamente las instrucciones sensibles a llamadas al hipervisor, mientras que las instrucciones no sensibles se ejecutan directamente en el hardware. Esto mejora el rendimiento al cachear los bloques traducidos.
* **Virtualización Asistida por Hardware**:
  * Requiere **CPUs con extensiones de virtualización** (ej. Intel VT, AMD SVM).
  * El VMM y las VMs se ejecutan en modos especiales (root-mode/non-root mode) que optimizan el manejo de instrucciones privilegiadas.
* **Paravirtualización**:
  * Involucra **modificaciones en el sistema operativo invitado** para mejorar el rendimiento.
  * El sistema operativo invitado, en lugar de ejecutar instrucciones sensibles, realiza **llamadas especiales (hypercalls)** a una API expuesta por el VMM.
  * El VMM no necesita realizar traducción binaria completa ni emular instrucciones de hardware, lo que simplifica la resolución de llamadas.
  * Puede ser implementada recompilando el kernel del sistema invitado o instalando drivers paravirtualizados (esta última es la técnica más común hoy en día para dispositivos como tarjetas de red o gráficas).
  * Ofrece **mejor rendimiento** que la virtualización completa, pero puede requerir un sistema operativo invitado modificado/específico.

## Implementaciones Destacadas

* **VM/370 (IBM)**: Un sistema operativo antiguo (1972) orientado a máquinas virtuales.
* **VMware Workstation**: Un hipervisor de tipo 2 que utiliza traducción binaria.
* **KVM**: Infraestructura de virtualización nativa del kernel de Linux (desde la versión 2.6.20), considerado tipo 1, que requiere extensiones de virtualización por hardware y soporta paravirtualización para algunos dispositivos.
* **ProxmoxVE**: Una distribución GNU/Linux basada en Debian que integra KVM como hipervisor y LXC para contenedores, además de QEMU. Es de código abierto y ofrece gestión web, migración en caliente y capacidades de clustering.
* **Hyper-V (Microsoft)**: Infraestructura de virtualización propietaria de Microsoft, considerada tipo 1 y embebida en el kernel de Windows Server y Windows 10 Pro.
* **VMware ESXi / vSphere**: Hipervisores de tipo 1 propietarios para entornos de servidores, líderes en el mercado empresarial.
* **Xen**: Infraestructura de virtualización de código abierto que actúa como hipervisor nativo y soporta paravirtualización, permitiendo la migración de VMs en caliente.
