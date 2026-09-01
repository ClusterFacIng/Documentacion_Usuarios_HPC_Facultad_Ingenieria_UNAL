# Manual de uso de Slurm

## 1. Objetivo

Este documento explica cómo preparar, enviar y monitorear trabajos en el HPC mediante **Slurm**. Está pensado para usuarios finales y refleja la configuración actual del clúster.

> **Importante:** todos los trabajos de cómputo deben ejecutarse a través de Slurm. Solicite solo los recursos que realmente necesita.

## 2. Configuración actual del HPC

Actualmente el clúster cuenta con cuatro nodos de cómputo:

```text
cnode01
cnode02
cnode03
cnode04
```

Cada nodo dispone de:

- 96 CPU lógicas.
- 2 sockets.
- 24 cores por socket.
- 2 hilos por core.
- 250000 MB de memoria RAM.

Particiones disponibles:

| Partición | Tiempo máximo | Predeterminada |
|---|---:|---|
| hpc-short | 1 hora | Sí |
| hpc | 24 horas | No |
| hpc-long | 72 horas | No |

Las particiones están configuradas con asignación exclusiva de recursos:

```text
OverSubscribe=EXCLUSIVE
```

## 3. Conceptos básicos

Un trabajo es una solicitud de recursos que Slurm administrará para ejecutar un programa.

```text
Usuario
  ↓
Script Slurm
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

## 4. Estructura básica de un script

Un script mínimo recomendado incluye directivas, carga de módulos y el comando de ejecución:

```bash
#!/bin/bash

#SBATCH --job-name=mi_trabajo
#SBATCH --partition=hpc-short
#SBATCH --time=00:30:00
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=1
#SBATCH --mem=2G

module purge
module load <aplicacion>

srun <comando>
```

## 5. Nombre del trabajo

```bash
#SBATCH --job-name=simulacion01
```

Esto permite identificar fácilmente el trabajo en la cola.

## 6. Selección de partición

```bash
#SBATCH --partition=hpc-short
```

Particiones disponibles:

```text
hpc-short
hpc
hpc-long
```

Recomendación de uso:

- `hpc-short`: pruebas y trabajos cortos.
- `hpc`: trabajos de duración media.
- `hpc-long`: simulaciones largas.

## 7. Solicitar tiempo

```bash
#SBATCH --time=HH:MM:SS
```

Ejemplos:

```bash
#SBATCH --time=00:30:00
#SBATCH --time=04:00:00
#SBATCH --time=48:00:00
```

El tiempo solicitado no puede superar el límite de la partición.

## 8. Solicitar tareas

```bash
#SBATCH --ntasks=8
```

Generalmente corresponde al número de procesos MPI.

## 9. CPU por tarea

```bash
#SBATCH --cpus-per-task=4
```

Ejemplo:

```bash
#SBATCH --ntasks=8
#SBATCH --cpus-per-task=4
```

Recursos solicitados:

```text
8 tareas × 4 CPU = 32 CPU
```

## 10. Solicitar nodos

También es posible solicitar explícitamente el número de nodos.

Forma larga:

```bash
#SBATCH --nodes=1
```

Forma corta:

```bash
#SBATCH -N 1
```

Ejemplo:

```bash
#SBATCH --nodes=2
```

## 11. Tareas por nodo

```bash
#SBATCH --ntasks-per-node=24
```

Ejemplo:

```bash
#SBATCH --nodes=2
#SBATCH --ntasks-per-node=24
```

Resultado:

```text
2 nodos × 24 tareas = 48 tareas
```

Esta opción es común en aplicaciones MPI.

## 12. Solicitar memoria

Se recomienda especificar siempre la memoria requerida.

```bash
#SBATCH --mem=8G
```

o:

```bash
#SBATCH --mem=8000M
```

## Importante

Cuando no se especifica memoria, Slurm puede reservar toda la memoria disponible del nodo.

Por esta razón se recomienda incluir siempre:

```bash
#SBATCH --mem=<cantidad>
```

## 13. Memoria por CPU

También puede solicitarse memoria por CPU:

```bash
#SBATCH --mem-per-cpu=1G
```

Ejemplo:

```bash
#SBATCH --ntasks=20
#SBATCH --mem-per-cpu=1G
```

Slurm reservará aproximadamente:

```text
20 × 1 GB = 20 GB
```

Esta opción es muy utilizada en aplicaciones MPI.

## 14. Archivos de salida y error

```bash
#SBATCH --output=resultado_%j.out
#SBATCH --error=resultado_%j.err
```

Variables útiles:

| Variable | Significado |
|---|---|
| %j | ID del trabajo |
| %x | Nombre del trabajo |

Ejemplo:

```bash
#SBATCH --job-name=simulacion
#SBATCH --output=%x-%j.out
#SBATCH --error=%x-%j.err
```

Generará:

```text
simulacion-123.out
simulacion-123.err
```

## 15. Uso de módulos

Antes de ejecutar una aplicación debe cargarse el módulo correspondiente.

```bash
module purge
module load openfoam/2412
```

Puede consultar los módulos disponibles con:

```bash
module avail
```

y los módulos cargados con:

```bash
module list
```

Consulte **[01-uso-modules-lmod.md](01-uso-modules-lmod.md)** para buscar y utilizar aplicaciones.

## 16. Aplicaciones que requieren configuración adicional

Algunas aplicaciones requieren inicialización adicional después de cargar el módulo.

Ejemplo con OpenFOAM:

```bash
module load openfoam/2412

source $FOAM_INST_DIR/openfoam2412/etc/bashrc
```

Consulte la documentación específica de cada aplicación.

## 17. Ejecución mediante `srun`

```bash
srun <comando>
```

Ejemplo:

```bash
srun ./mi_programa
```

Se recomienda utilizar `srun` dentro de los scripts para ejecutar aplicaciones utilizando los recursos asignados por Slurm.

## 18. Uso interactivo con `srun`

Para realizar pruebas rápidas:

```bash
srun \
  --partition=hpc-short \
  --time=00:30:00 \
  --ntasks=1 \
  --cpus-per-task=4 \
  --mem=4G \
  --pty bash
```

Esto abrirá una terminal interactiva cuando Slurm asigne recursos.

## 19. Solicitar recursos interactivos con `salloc`

También es posible reservar recursos mediante:

```bash
salloc \
  --partition=hpc-short \
  --time=00:30:00 \
  --ntasks=1 \
  --cpus-per-task=4 \
  --mem=4G
```

Una vez obtenida la asignación:

```bash
srun ./mi_programa
```

## 20. MPI: `srun` y `mpirun`

Dependiendo de la aplicación pueden utilizarse ambos métodos.

Con `srun`:

```bash
srun ./mi_programa
```

Con `mpirun`:

```bash
mpirun ./mi_programa
```

Muchos ejemplos del HPC utilizan OpenMPI mediante `mpirun`.

Consulte la documentación específica de cada aplicación.

## 21. Comandos normales dentro del script

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

## 22. Ejemplo básico

```bash
#!/bin/bash

#SBATCH --job-name=prueba
#SBATCH --partition=hpc-short
#SBATCH --time=00:30:00
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=1
#SBATCH --mem=2G

module purge
module load <aplicacion>

srun ./mi_programa
```

Enviar:

```bash
sbatch trabajo.slurm
```

## 23. Ejemplo MPI

```bash
#!/bin/bash

#SBATCH --job-name=mpi_test
#SBATCH --partition=hpc-long
#SBATCH --time=48:00:00
#SBATCH --ntasks=20
#SBATCH --mem-per-cpu=1G
#SBATCH --output=%x-%j.out
#SBATCH --error=%x-%j.err

module purge
module load <aplicacion>

mpirun ./mi_programa
```

## 24. Ejemplo OpenFOAM

```bash
#!/bin/bash

#SBATCH --job-name=prepare
#SBATCH --partition=hpc
#SBATCH --time=01:00:00
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --mem=4G

module load openfoam/2412

source $FOAM_INST_DIR/openfoam2412/etc/bashrc
source $WM_PROJECT_DIR/bin/tools/RunFunctions

blockMesh

decomposePar -force
```

## 25. Enviar trabajos

```bash
sbatch trabajo.slurm
```

Respuesta típica:

```text
Submitted batch job 12345
```

## 26. Consultar trabajos

Todos los trabajos del usuario:

```bash
squeue -u $USER
```

Trabajo específico:

```bash
squeue -j <jobid>
```

## 27. Estados comunes

| Estado | Significado |
|---|---|
| PD | Pendiente |
| R | Ejecutándose |
| CG | Finalizando |
| CD | Completado |
| F | Fallido |
| CA | Cancelado |

## 28. Información detallada de un trabajo

```bash
scontrol show job <jobid>
```

Esto permite consultar estado, tiempo solicitado, memoria, CPUs, directorio de trabajo, archivos de salida y razones de espera.

## 29. Razones comunes de espera

| Razón | Significado |
|---|---|
| Resources | No hay recursos disponibles |
| Priority | Esperando turno |
| ReqNodeNotAvail | Nodo solicitado no disponible |
| Nodes_required_for_job_are_DOWN | Los nodos requeridos están apagados o no responden |

## 30. Consultar particiones y nodos

```bash
sinfo
```

Vista resumida:

```bash
sinfo -o "%P %a %l %D %C"
```

También puede revisar nodos y particiones con:

```bash
sinfo -N
scontrol show nodes
scontrol show partition
```

## 31. Variables útiles de Slurm

| Variable | Descripción |
|---|---|
| $SLURM_JOB_ID | ID del trabajo |
| $SLURM_JOB_NAME | Nombre del trabajo |
| $SLURM_JOB_NODELIST | Nodos asignados |
| $SLURM_NTASKS | Número de tareas |
| $SLURM_CPUS_PER_TASK | CPU por tarea |
| $SLURM_JOB_PARTITION | Partición utilizada |

Ejemplo:

```bash
echo "Job ID: $SLURM_JOB_ID"
echo "Partición: $SLURM_JOB_PARTITION"
```

## 32. Cancelar trabajos

```bash
scancel <jobid>
```

Ejemplo:

```bash
scancel 12345
```

## 33. Buenas prácticas

- Solicitar únicamente los recursos necesarios.
- Solicitar siempre la memoria requerida.
- Elegir la partición adecuada.
- Solicitar tiempos realistas.
- Cargar módulos dentro del script.
- Utilizar `srun` para ejecutar aplicaciones cuando sea apropiado.
- Consultar el estado mediante `squeue`.
- Utilizar `scontrol show job` para diagnosticar problemas.
- Utilizar `srun` o `salloc` para pruebas interactivas.
- Evitar solicitar recursos excesivos.

## 34. Flujo recomendado

```text
Preparar script
      ↓
Solicitar CPU, memoria y tiempo
      ↓
Cargar módulos
      ↓
Enviar con sbatch
      ↓
Consultar con squeue
      ↓
Ejecución
      ↓
Revisar resultados
      ↓
Diagnóstico con scontrol
```

## 35. Resumen rápido

### Enviar

```bash
sbatch trabajo.slurm
```

### Consultar cola

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

### Ver particiones

```bash
sinfo
```

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
