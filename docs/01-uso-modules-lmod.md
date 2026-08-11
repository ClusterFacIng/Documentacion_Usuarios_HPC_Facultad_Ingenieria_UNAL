# Manual de uso de Modules (Lmod)

## 1. Objetivo

Este manual explica cómo consultar y utilizar las aplicaciones disponibles en el HPC mediante **Modules (Lmod)**.

El sistema de módulos permite seleccionar las aplicaciones y versiones que ya han sido instaladas por los administradores del HPC.

> **Importante:** los usuarios no deben instalar software ni modificar las instalaciones del sistema. Si una aplicación no está disponible, debe solicitarse al administrador del HPC.

## 2. ¿Qué es `module`?

`module` permite activar una aplicación dentro de la sesión de trabajo.

```bash
module load openfoam/2506
```

Esto **no instala OpenFOAM**. Simplemente configura el entorno para utilizar la versión que ya está instalada en el HPC.

## 3. Ver las aplicaciones disponibles

```bash
module avail
```

Para buscar una aplicación específica:

```bash
module spider <aplicacion>
```

Ejemplo:

```bash
module spider openfoam
```

`module spider` también permite consultar las versiones disponibles y, cuando corresponde, información sobre los requisitos de la aplicación.

## 4. Cargar una aplicación

```bash
module load <aplicacion>
```

Para una versión específica:

```bash
module load <aplicacion>/<version>
```

Ejemplo:

```bash
module load openfoam/2506
```

Después:

```bash
module list
```

## 5. Verificar una aplicación

```bash
which <comando>
```

y, cuando la aplicación lo permita:

```bash
<comando> --version
```

Ejemplo:

```bash
module load cmake
which cmake
cmake --version
```

## 6. Consultar información de un módulo

```bash
module show <modulo>
```

Ejemplo:

```bash
module show openmpi5
```

## 7. Ver los módulos cargados

```bash
module list
```

## 8. Quitar una aplicación

```bash
module unload <modulo>
```

## 9. Limpiar el entorno

```bash
module purge
```

Se recomienda utilizarlo antes de preparar un entorno nuevo.

```bash
module purge
module load gnu14
module load openmpi5
```

## 10. Utilizar una versión específica

Cuando existen varias versiones, se recomienda indicar explícitamente la versión en trabajos que deban ser reproducibles:

```bash
module load openfoam/2506
```

## 11. Aplicaciones con dependencias

Algunas aplicaciones requieren otros componentes para funcionar. Lmod puede gestionar estas relaciones.

Para consultar los requisitos:

```bash
module spider <aplicacion>
```

No se deben cargar módulos adicionales sin necesidad.

## 12. Uso de Modules con Slurm

Cuando una aplicación se ejecuta mediante un trabajo de Slurm, los módulos necesarios deben cargarse **dentro del script del trabajo**.

```bash
#!/bin/bash

#SBATCH --job-name=mi_trabajo
#SBATCH --partition=hpc-short
#SBATCH --time=00:30:00

module purge
module load <aplicacion>/<version>

srun <comando>
```

La configuración de recursos y las opciones de Slurm se explican en **[02-uso-slurm.md](02-uso-slurm.md)**. Para comprender cuándo es adecuado realizar una prueba interactiva y cuándo se debe pasar a un trabajo de Slurm, consulte también **[03-uso-interactivo-de-aplicaciones.md](03-uso-interactivo-de-aplicaciones.md)** y **[04-ejecucion-de-aplicaciones-y-computo-con-slurm.md](04-ejecucion-de-aplicaciones-y-computo-con-slurm.md)**.

> **Advertencia:** la ejecución interactiva solo debe usarse para revisar que la aplicación responda correctamente antes de enviar un trabajo grande. No reemplaza el uso de Slurm para actividades de cómputo reales.

## 13. Guardar un entorno

```bash
module save mi_entorno
```

Para restaurarlo:

```bash
module restore mi_entorno
```

Para consultar las colecciones:

```bash
module savelist
```

## 14. Si una aplicación no está disponible

Primero:

```bash
module avail
```

y después:

```bash
module spider <aplicacion>
```

Si tampoco aparece, es posible que no esté instalada o no esté disponible para usuarios.

- No intente instalarla globalmente.
- No modifique los directorios de software del sistema.
- Solicite al administrador del HPC la instalación o habilitación de la aplicación.

## 15. Comandos de uso frecuente

| Comando | Función |
|---|---|
| `module avail` | Ver módulos disponibles |
| `module spider <aplicacion>` | Buscar una aplicación y sus versiones |
| `module load <modulo>` | Cargar una aplicación |
| `module unload <modulo>` | Quitar una aplicación |
| `module list` | Ver módulos cargados |
| `module purge` | Limpiar los módulos cargados |
| `module show <modulo>` | Consultar información del módulo |
| `module save <nombre>` | Guardar un entorno |
| `module restore <nombre>` | Restaurar un entorno |
| `module savelist` | Ver entornos guardados |

## 16. Flujo rápido

```bash
module spider <aplicacion>
module purge
module load <aplicacion>/<version>
module list
which <comando>
```

Después puede utilizarse la aplicación en una sesión interactiva o dentro de un trabajo de Slurm.

## 17. Buenas prácticas

- Buscar primero la aplicación con `module spider`.
- Utilizar `module purge` antes de preparar un entorno nuevo.
- Cargar únicamente las aplicaciones necesarias.
- Utilizar versiones específicas cuando se requiera reproducibilidad.
- Cargar los módulos dentro del script de Slurm.
- No modificar ni eliminar software instalado por los administradores.
- Solicitar al administrador las aplicaciones que no estén disponibles.

## 18. Resumen

El uso habitual de Modules es:

```text
Buscar → Cargar → Verificar → Utilizar
```

Ejemplo:

```bash
module spider openfoam
module purge
module load openfoam/2506
module list
```
