# Ejecución de aplicaciones en el HPC

## 1. Objetivo

Este documento sirve como introducción general para ejecutar aplicaciones en el HPC de forma ordenada y adecuada al tipo de tarea que se va a realizar.

## 2. ¿Cómo ejecutar una aplicación en el HPC?

El proceso general es el siguiente:

1. Identificar la aplicación que se desea utilizar.
2. Consultar si está disponible mediante [01-uso-modules-lmod.md](01-uso-modules-lmod.md).
3. Cargar los módulos necesarios para el entorno.
4. Probar la aplicación de forma interactiva solo cuando se necesite verificar que responda correctamente.
5. Si la tarea implica cómputo real, enviarla mediante [02-uso-slurm.md](02-uso-slurm.md) para ejecutarla en los nodos de cómputo.

## 3. Regla general

No todas las ejecuciones deben hacerse de la misma manera.

- Para tareas pequeñas de prueba o verificación, puede usarse una sesión interactiva.
- Para actividades de cómputo, simulaciones, procesamiento intensivo o trabajos que requieran recursos, la ejecución debe hacerse obligatoriamente con Slurm.

!!! important "Advertencia"
    La ejecución interactiva solo es apropiada para revisar que la aplicación responda correctamente antes de enviar un trabajo grande. No sustituye el uso de Slurm para trabajos de cómputo reales.

## 4. Flujo recomendado

Un flujo simple sería:

1. Buscar y cargar la aplicación con Modules.
2. Realizar una prueba breve para verificar que el comando funcione.
3. Si la tarea es de cómputo, preparar un script de Slurm.
4. Enviar el trabajo y monitorearlo.

## 5. Recursos recomendados

- Para conocer cómo cargar y consultar aplicaciones, consulte [01-uso-modules-lmod.md](01-uso-modules-lmod.md).
- Para aprender a preparar y enviar trabajos con recursos definidos, consulte [02-uso-slurm.md](02-uso-slurm.md).
- Para entender mejor cuándo usar la ejecución interactiva y cuándo evitarla, consulte [03-uso-interactivo-de-aplicaciones.md](03-uso-interactivo-de-aplicaciones.md).

## 6. Resumen

La idea central es sencilla:

- usar Modules para preparar el entorno;
- usar Slurm para ejecutar trabajos de cómputo;
- reservar la ejecución interactiva solo para pruebas rápidas y verificación inicial.
