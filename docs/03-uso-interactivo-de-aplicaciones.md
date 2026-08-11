# Uso interactivo de aplicaciones en el HPC

## 1. Objetivo

Este manual explica cuándo es apropiado ejecutar una aplicación de forma interactiva en el HPC y cuándo se debe recurrir a Slurm para trabajos de cómputo.

## 2. ¿Qué significa ejecutar de forma interactiva?

La ejecución interactiva consiste en iniciar una aplicación directamente desde la sesión abierta en el HPC, sin enviar un trabajo a Slurm.

Este tipo de ejecución es útil para:

- verificar que una aplicación esté instalada correctamente;
- probar comandos simples;
- revisar la salida de un programa pequeño;
- realizar tareas de configuración o depuración inicial.

!!! warning "Importante"
    La ejecución interactiva solo es recomendable para tareas muy pequeñas y de prueba. Para cualquier actividad de cómputo, simulación, procesamiento intensivo, compilación larga o uso de múltiples recursos, se debe usar Slurm.

## 3. Recomendación general

Si bien es posible ejecutar una aplicación de forma interactiva, esta práctica debe limitarse a actividades pequeñas.

Para actividades de cómputo reales, se debe hacer uso de Slurm obligatoriamente para ejecutar la aplicación en los nodos de cómputo.

## 4. Ejemplo de uso interactivo

Primero se cargan los módulos necesarios:

```bash
module purge
module load python/3.11
```

Luego se puede ejecutar una prueba sencilla:

```bash
python --version
python mi_script.py
```

Este tipo de ejecución sirve para comprobar que el programa funciona y para revisar resultados preliminares.

## 5. Cuándo no usar ejecución interactiva

No se recomienda ejecutar de forma interactiva cuando:

- el programa tarda mucho tiempo en completarse;
- se necesita más memoria de la disponible en la sesión actual;
- se requieren múltiples CPU o varios procesos;
- el trabajo debe ejecutarse de forma reproducible y controlada;
- la tarea corresponde a un cálculo de alto rendimiento.

En esos casos, la ejecución debe enviarse a Slurm.

## 6. Buenas prácticas

- Usar la sesión interactiva únicamente para pruebas rápidas.
- Cargar únicamente los módulos necesarios.
- Revisar que la aplicación responda correctamente antes de enviar un trabajo grande.
- Si el trabajo va a consumir recursos importantes, prepararlo como script de Slurm.
- Evitar ejecutar procesos largos en la sesión de acceso.

## 7. Resumen

La ejecución interactiva es útil para pruebas pequeñas, pero no es la forma apropiada para actividades de cómputo reales.

Para trabajos serios, largos o con requerimientos de recursos, se debe usar Slurm de forma obligatoria.
