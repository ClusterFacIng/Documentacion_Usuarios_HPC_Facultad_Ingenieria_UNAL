# Manual de uso de Slurm

## 1. Objetivo

Este manual explica cómo solicitar recursos, enviar, consultar y cancelar trabajos en el HPC mediante **Slurm**.

Está dirigido a usuarios del HPC y utiliza la configuración actualmente definida para el cluster.

> **Importante:** los usuarios deben ejecutar los trabajos de cómputo mediante Slurm y solicitar únicamente los recursos que necesitan.

## 2. Configuración actual del HPC

Actualmente el cluster cuenta con cuatro nodos:

```text
cnode01
cnode02
cnode03
cnode04
```

Cada nodo está configurado con:

- 96 CPU lógicas.
- 2 sockets.
- 24 cores por socket.
- 2 hilos por core.
- 250000 MB de memoria.
- Particiones `hpc`, `hpc-short` y `hpc-long`.

| Partición | Tiempo máximo | Predeterminada |
|---|---:|---|
| `hpc-short` | 1 hora | Sí |
| `hpc` | 24 horas | No |
| `hpc-long` | 72 horas | No |

Las particiones están configuradas con `Oversubscribe=EXCLUSIVE`.

## 3. ¿Qué es un trabajo de Slurm?

Un trabajo es una solicitud de recursos al HPC para ejecutar un programa.

```text
Usuario
  ↓
Script
  ↓
sbatch
  ↓
Slurm
  ↓
Recursos asignados
  ↓
Ejecución
```

El usuario no necesita seleccionar manualmente un nodo para un trabajo normal.

## 4. Crear un script

Un script básico:

```bash
#!/bin/bash

#SBATCH --job-name=mi_trabajo
#SBATCH --partition=hpc-short
#SBATCH --time=00:30:00

module purge
module load <aplicacion>/<version>

srun <comando>
```

El script puede contener:

1. Opciones `#SBATCH`.
2. Preparación del entorno.
3. Carga de módulos.
4. Comandos normales de Linux.
5. `srun` para ejecutar el programa.

## 5. Nombre del trabajo

```bash
#SBATCH --job-name=mi_trabajo
```

Ejemplo:

```bash
#SBATCH --job-name=simulacion01
```

## 6. Seleccionar una partición

```bash
#SBATCH --partition=<particion>
```

Particiones disponibles:

```text
hpc-short
hpc
hpc-long
```

Uso recomendado:

- `hpc-short`: hasta 1 hora.
- `hpc`: hasta 24 horas.
- `hpc-long`: hasta 72 horas.

## 7. Solicitar tiempo

```bash
#SBATCH --time=HH:MM:SS
```

Ejemplo:

```bash
#SBATCH --time=00:30:00
```

El tiempo solicitado no puede superar el límite de la partición.

## 8. Solicitar tareas

```bash
#SBATCH --ntasks=4
```

Para un programa paralelo, `--ntasks` suele representar el número de procesos que se ejecutarán.

Después:

```bash
srun ./mi_programa
```

## 9. CPU por tarea

```bash
#SBATCH --ntasks=4
#SBATCH --cpus-per-task=2
```

Esto solicita 4 tareas con 2 CPU por tarea, es decir, 8 CPU en total.

## 10. Solicitar memoria

```bash
#SBATCH --mem=8G
```

También se puede especificar en MB:

```bash
#SBATCH --mem=8000M
```

## 11. Salida y errores

```bash
#SBATCH --output=resultado_%j.out
#SBATCH --error=error_%j.err
```

`%j` se reemplaza por el identificador del trabajo.

## 12. Cargar aplicaciones mediante Modules

Dentro del script:

```bash
module purge
module load <aplicacion>/<version>
```

Ejemplo:

```bash
module purge
module load openfoam/2506
```

Consulte **[01-uso-modules-lmod.md](01-uso-modules-lmod.md)** para buscar y utilizar aplicaciones. Si desea conocer cuándo es apropiado hacer una prueba interactiva antes de enviar un trabajo, consulte **[03-uso-interactivo-de-aplicaciones.md](03-uso-interactivo-de-aplicaciones.md)** y **[04-ejecucion-de-aplicaciones-y-computo-con-slurm.md](04-ejecucion-de-aplicaciones-y-computo-con-slurm.md)**.

> **Advertencia:** el uso interactivo solo es recomendable para revisar que la aplicación responda correctamente antes de enviar un trabajo grande. Para actividades de cómputo reales, la ejecución debe realizarse obligatoriamente mediante Slurm.

## 13. Ejecutar un programa con `srun`

```bash
srun <comando>
```

Ejemplo:

```bash
#SBATCH --ntasks=4

srun ./mi_programa
```

## 14. Comandos normales dentro del script

También pueden utilizarse comandos normales de Linux:

```bash
cd ~/proyecto
mkdir -p resultados
cp entrada.dat resultados/
echo "Iniciando simulación"
```

También variables de entorno:

```bash
export OMP_NUM_THREADS=4
```

Estos comandos pueden ejecutarse antes de `srun`.

## 15. Ejemplo de trabajo corto

```bash
#!/bin/bash

#SBATCH --job-name=prueba
#SBATCH --partition=hpc-short
#SBATCH --time=00:30:00
#SBATCH --ntasks=1
#SBATCH --mem=2G

module purge
module load <aplicacion>/<version>

module list

srun ./mi_programa
```

Enviar:

```bash
sbatch trabajo.slurm
```

## 16. Ejemplo de trabajo paralelo

```bash
#!/bin/bash

#SBATCH --job-name=mpi_test
#SBATCH --partition=hpc
#SBATCH --time=02:00:00
#SBATCH --ntasks=8
#SBATCH --mem=8G

module purge
module load gnu14
module load openmpi5

module list

srun ./mi_programa
```

## 17. Ejemplo de trabajo multihilo

```bash
#!/bin/bash

#SBATCH --job-name=multihilo
#SBATCH --partition=hpc-short
#SBATCH --time=00:30:00
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=8
#SBATCH --mem=8G

module purge
module load <aplicacion>/<version>

export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK

srun ./mi_programa
```

## 18. Enviar un trabajo

```bash
sbatch trabajo.slurm
```

Slurm devolverá un identificador:

```text
Submitted batch job 12345
```

## 19. Consultar trabajos

```bash
squeue -u $USER
```

Trabajo específico:

```bash
squeue -j <jobid>
```

## 20. Estados comunes

| Estado | Significado |
|---|---|
| `PD` | Pendiente |
| `R` | Ejecutándose |
| `CG` | Finalizando |
| `CD` | Completado |
| `F` | Fallido |
| `CA` | Cancelado |

## 21. Cancelar un trabajo

```bash
scancel <jobid>
```

Ejemplo:

```bash
scancel 12345
```

Para cancelar todos los trabajos propios:

```bash
scancel -u $USER
```

Utilice esta opción con cuidado.

## 22. Consultar las particiones

```bash
sinfo
```

Vista resumida:

```bash
sinfo -o "%P %a %l %D %C"
```

## 23. Consultar información de un trabajo

```bash
scontrol show job <jobid>
```

## 24. Consultar trabajos terminados

```bash
sacct
```

Trabajo específico:

```bash
sacct -j <jobid>
```

También:

```bash
sacct -j <jobid> --format=JobID,JobName,Partition,State,Elapsed,AllocCPUS,MaxRSS
```

## 25. Revisar el uso de recursos

Después de ejecutar un trabajo:

```bash
sacct -j <jobid> --format=JobID,State,Elapsed,AllocCPUS,MaxRSS
```

Esto ayuda a comprobar si los recursos solicitados fueron adecuados.

## 26. Variables útiles de Slurm

| Variable | Información |
|---|---|
| `$SLURM_JOB_ID` | Identificador del trabajo |
| `$SLURM_JOB_NAME` | Nombre del trabajo |
| `$SLURM_JOB_NODELIST` | Nodos asignados |
| `$SLURM_NTASKS` | Número de tareas |
| `$SLURM_CPUS_PER_TASK` | CPU por tarea |
| `$SLURM_JOB_PARTITION` | Partición utilizada |

Ejemplo:

```bash
echo "Job ID: $SLURM_JOB_ID"
echo "Partición: $SLURM_JOB_PARTITION"
echo "Tareas: $SLURM_NTASKS"
```

## 27. Buenas prácticas

- Solicitar únicamente los recursos necesarios.
- Elegir la partición según la duración real del trabajo.
- Solicitar un tiempo razonable.
- Cargar los módulos dentro del script.
- Utilizar `srun` para ejecutar el programa con los recursos asignados.
- Revisar el consumo con `sacct`.
- No seleccionar manualmente un nodo para trabajos normales.

## 28. Flujo recomendado

```text
Preparar script
      ↓
Solicitar recursos
      ↓
Cargar módulos
      ↓
Enviar con sbatch
      ↓
Consultar con squeue
      ↓
Ejecutar
      ↓
Revisar resultados
      ↓
Consultar consumo con sacct
```

## 29. Ejemplo completo

```bash
#!/bin/bash

#SBATCH --job-name=simulacion
#SBATCH --partition=hpc
#SBATCH --time=04:00:00
#SBATCH --ntasks=8
#SBATCH --mem=16G
#SBATCH --output=simulacion_%j.out
#SBATCH --error=simulacion_%j.err

module purge
module load gnu14
module load openmpi5

echo "Job ID: $SLURM_JOB_ID"
echo "Partición: $SLURM_JOB_PARTITION"
echo "Tareas: $SLURM_NTASKS"

module list

cd ~/proyecto

srun ./mi_programa
```

Enviar:

```bash
sbatch simulacion.slurm
```

Consultar:

```bash
squeue -u $USER
```

Cuando termine:

```bash
sacct -j <jobid>
```

## 30. Referencia rápida

### Enviar

```bash
sbatch trabajo.slurm
```

### Consultar

```bash
squeue -u $USER
```

### Ver información

```bash
scontrol show job <jobid>
```

### Cancelar

```bash
scancel <jobid>
```

### Revisar un trabajo terminado

```bash
sacct -j <jobid>
```

### Ver particiones

```bash
sinfo
```

## 31. Importante

- Los trabajos de cómputo deben enviarse mediante Slurm.
- No se debe seleccionar manualmente un nodo para un trabajo normal.
- Se debe elegir la partición de acuerdo con la duración del trabajo.
- No se deben solicitar más CPU, memoria o tiempo del necesario.
- Los módulos deben cargarse dentro del script.
- La aplicación debe ejecutarse utilizando los recursos solicitados.
