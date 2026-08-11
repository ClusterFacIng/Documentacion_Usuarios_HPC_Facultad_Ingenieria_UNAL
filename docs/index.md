# Documentación de acceso y uso del HPC

Esta documentación reúne las guías para conectarse, configurar el entorno y utilizar correctamente el clúster HPC de la Facultad de Ingeniería de la Universidad Nacional de Colombia.

## Contenido

### Acceso y conexión

- [Guía para Windows](guia-windows.md)
- [Guía para Linux](guia-linux.md)

### Uso de aplicaciones y recursos del HPC

- [Ejecución de aplicaciones en el HPC](04-ejecucion-de-aplicaciones-y-computo-con-slurm.md)
- [Manual de uso de Modules (Lmod)](01-uso-modules-lmod.md)
- [Manual de uso de Slurm](02-uso-slurm.md)
- [Uso interactivo de aplicaciones en el HPC](03-uso-interactivo-de-aplicaciones.md)

## Antes de comenzar

- Cuenta institucional activa de la Universidad Nacional.
- Acceso a Internet.
- Autorización previa para usar la VPN y el HPC.
- Conocimiento básico del uso de terminal y comandos de Linux.

## Flujo general recomendado

1. Solicitar y obtener acceso al sistema.
2. Conectarse a la VPN institucional.
3. Configurar el cliente SSH según el sistema operativo.
4. Ingresar al HPC y verificar la disponibilidad de módulos.
5. Usar Modules para cargar aplicaciones.
6. Ejecutar tareas pequeñas de forma interactiva solo cuando sea necesario.
7. Para trabajos de cómputo, enviar siempre los procesos mediante Slurm en los nodos de cómputo.

## Objetivo de esta guía

Brindar una referencia clara para que los usuarios puedan:

- conectarse al HPC;
- consultar y cargar aplicaciones con Modules;
- preparar y enviar trabajos con Slurm;
- ejecutar correctamente aplicaciones y tareas de cómputo de forma segura y ordenada.
