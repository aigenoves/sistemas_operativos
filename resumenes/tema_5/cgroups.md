# Grupos de Control de Linux: Cgroups v1 y v2

Los **Control Groups (cgroups)** son una característica del kernel de Linux que permite **organizar procesos en grupos jerárquicos para limitar y monitorear el uso de diversos tipos de recursos.**
Su desarrollo comenzó en Google en 2006 bajo el nombre de "process containers", y fue renombrado a "Control Groups" en 2007, estando disponible desde la versión 2.6.24 del kernel. Actualmente, Tejun Heo es el encargado de su desarrollo y mantenimiento.

## Capacidades y Propósitos de los Cgroups

- **Resource Limiting (Limitación de Recursos):** Evita que los grupos excedan el uso de un recurso específico, como tiempo de CPU, cantidad de CPUs, memoria o I/O.
- **Prioritization (Priorización):** Permite que un grupo obtenga prioridad en el uso de ciertos recursos, como tiempo de CPU o I/O.
- **Accounting (Contabilidad):** Facilita la medición del uso de recursos por parte de un grupo, lo que es útil para estadísticas, monitoreo o facturación (billing).
- **Control: Permite "freezar"** (congelar) y reiniciar un grupo de procesos.

Los procesos desconocen los límites que les aplica un cgroup.

## Implementación y Mecanismo

- La interfaz de cgroups del kernel se provee a través de un **pseudo-filesystem llamado ``cgroups``.**
- Un **``cgroup``** asocia un conjunto de procesos con parámetros o límites para uno o más subsistemas.
- Un **subsistema** (también llamado resource controller o controller) es un componente del kernel que modifica el comportamiento de los procesos en un cgroup. Actualmente, hay 12 subsistemas definidos.
- Los cgroups se organizan en una **jerarquía**, donde la estructura de directorios refleja la dependencia entre ellos. Cada cgroup se representa con un directorio.
- En cada nivel de la jerarquía se pueden definir atributos (límites) que los cgroups hijos no pueden exceder.
- Un proceso creado mediante ``fork`` pertenece al mismo cgroup que su padre.
- Cada directorio de cgroup contiene archivos que pueden ser escritos/leídos, y una vez definidos los grupos, se les pueden agregar IDs de procesos.

## Versiones de Cgroups

Existen dos versiones principales, cgroups v1 y cgroups v2, que pueden coexistir en el mismo sistema.

- **Cgroups v1:**

  - Los distintos controladores se fueron agregando de manera descoordinada, haciendo la administración de las jerarquías más compleja.
  - Cada subsistema (controlador) puede ser montado en pseudo-filesystems individuales o en un mismo pseudo-filesystem, comúnmente en ``/sys/fs/cgroup``.
  - Un proceso solo puede pertenecer a un cgroup dentro de una jerarquía de un subsistema, pero puede pertenecer a varias jerarquías (es decir, a un cgroup en la jerarquía de CPU, y a otro en la de memoria).
  - Es posible asignar threads de un proceso a diferentes cgroups.

- **Cgroups v2:**

  - Pensado como un reemplazo de cgroups v1.
  - Todos los controladores se montan en una jerarquía unificada.
  - No permite procesos internos en un cgroup, a excepción del cgroup raíz. Los procesos deben asignarse a cgroups que no tienen hijos (cgroups "hoja").
  - Cada cgroup en v2 contiene archivos como ``cgroup.controllers`` (lista de controladores disponibles) y cgroup.subtree_control (indica qué controladores se habilitarán en los cgroups hijos).
  - Los archivos correspondientes a un controlador habilitado se generan automáticamente al crear un cgroup hijo.
  - Incluye el archivo ``cgroup.events`` para notificar sobre el estado del cgroup (si tiene procesos, si está congelado).

**Uso con Contenedores:** Los cgroups son una de las características clave del kernel que Docker y Podman utilizan para proveer contenedores. Esto permite **limitar opcionalmente los recursos asignados a un contenedor**. En el contexto de Podman, los cgroups se utilizan a bajo nivel para el aislamiento de los contenedores/pods.