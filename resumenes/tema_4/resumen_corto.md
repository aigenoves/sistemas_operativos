# 📝 Resumen Corto de Virtualización

## ✅ ¿Qué es la Virtualización?

- Técnica de abstracción de recursos como CPU, red, almacenamiento.
- Capa que desacopla hardware físico del sistema operativo.
- Permite múltiples entornos virtuales sobre una sola máquina física.

## 🔠 Tipos de Virtualización

- **Proceso**: Ej. JVM.
- **Almacenamiento**: Ej. RAID, LVM.
- **Red**: Virtualización de dispositivos de red.
- **Sistema Operativo (Contenedores)**: Espacios de usuario aislados.
- **Sistema (Máquinas Virtuales)**: VMs completas.

## 🎯 Ventajas

- Uso eficiente del hardware.
- Compatibilidad con software legacy.
- Pruebas seguras en entornos aislados.
- Simulación de redes y entornos limitados.
- Migración, respaldo y monitoreo facilitados.

## 🧠 Conceptos Clave

- **Host**: Software que ejecuta la VM.
- **Guest**: SO que se virtualiza.
- **Hipervisor / VMM**: Controla VMs y recursos.

## 🔒 Características de VMs (Popek y Goldberg, 1974)

- **Equivalencia**: Mismo comportamiento que en hardware real.
- **Control de recursos**.
- **Eficiencia**: La mayoría de instrucciones se ejecutan directamente.

## ⚙️ Instrucciones Sensibles vs Privilegiadas

- x86 no cumple totalmente con el teorema de virtualización.
- Necesario que las instrucciones sensibles ⊆ privilegiadas.

## 🛡️ Anillos de Privilegios (x86)

- **Ring 0**: Kernel (hipervisor).
- **Ring 3**: Usuario (guest OS).

## 🧱 Tipos de Hipervisores

- **Tipo 1 (Bare-metal)**: Directo sobre hardware. Ej: KVM, Xen.
- **Tipo 2 (Hosted)**: Corre sobre un OS. Ej: VirtualBox.

## 🛠️ Técnicas

- **Emulación**: Hardware simulado en software (lento).
- **Virtualización Completa**: Sin modificar OS invitado.
- **Virtualización Asistida**: Usa extensiones de CPU (Intel VT, AMD SVM).
- **Paravirtualización**: Guest OS modificado con hypercalls.

## 🚀 Implementaciones Destacadas

- **KVM**: En el kernel de Linux.
- **ProxmoxVE**: KVM + LXC + gestión web.
- **VMware Workstation / ESXi**: Comercial, alto rendimiento.
- **Xen**: Código abierto, soporta migración en caliente.
- **Hyper-V**: Microsoft, tipo 1.

---
Para más detalles, revisar el archivo [`README.md`][readme] completo.

[readme]: https://github.com/aigenoves/sistemas_operativos/blob/main/resumenes/tema_4/README.md
