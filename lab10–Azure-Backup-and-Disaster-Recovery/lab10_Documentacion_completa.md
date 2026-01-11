# Lab 10 – Implementar Azure Backup y Recuperación ante Desastres (AZ-104)

## Introducción

En este laboratorio se implementan soluciones de **protección de datos y recuperación ante desastres** en Azure utilizando **Recovery Services Vault**, **Azure Backup** y **Azure Site Recovery**.

Se despliega una máquina virtual mediante una **plantilla ARM**, se configura la **copia de seguridad a nivel de VM**, se habilita la **monitorización mediante diagnósticos**, y finalmente se implementa la **replicación entre regiones** para escenarios de recuperación ante desastres.

---

## Escenario de negocio

La organización necesita proteger una máquina virtual crítica frente a:
- Pérdida accidental de datos  
- Eliminaciones no intencionadas  
- Fallos regionales  

Para ello, se requiere:
- Copias de seguridad automáticas con retención definida  
- Monitorización centralizada de los trabajos de backup  
- Replicación de la máquina virtual a una región secundaria  

---

## Objetivos del laboratorio

- Desplegar infraestructura mediante plantillas ARM  
- Crear y configurar una Recovery Services Vault  
- Implementar Azure Backup a nivel de máquina virtual  
- Monitorizar Azure Backup con cuentas de almacenamiento  
- Habilitar replicación de máquinas virtuales entre regiones  

---

## Tarea 1 – Desplegar infraestructura con plantilla ARM

### 1.1 Implementar una plantilla personalizada

Desde el **Portal de Azure**, accedemos a:

> Implementar una plantilla personalizada → Crear tu propia plantilla en el editor

![1.1](screenshots/1.1.png)

---

### 1.2 Cargar la plantilla ARM

Pegamos o cargamos la **plantilla ARM** proporcionada para el laboratorio, que despliega la red virtual y la máquina virtual que se utilizará para las pruebas de backup.

![1.2](screenshots/1.2.png)

---

### 1.3 Cargar el archivo de parámetros

Cargamos el archivo de **parámetros ARM**, donde se definen valores como el tamaño de la VM y el usuario administrador.

![1.3](screenshots/1.3.png)

---

### 1.4 Revisar y desplegar

Revisamos los detalles del despliegue, seleccionamos el **grupo de recursos** y definimos una **contraseña segura** para la máquina virtual.

![1.4](screenshots/1.4.png)

---

## Tarea 2 – Crear y configurar una Recovery Services Vault

### 2.1 Crear una nueva bóveda

Accedemos a **Recovery Services vaults** y seleccionamos **Crear**.

![2.1](screenshots/2.1.png)

---

### 2.2 Configuración básica

Definimos el **grupo de recursos**, la **región** y el **nombre de la bóveda**.

![2.2](screenshots/2.2.png)

---

### 2.3 Crear la bóveda

Revisamos la configuración y creamos la Recovery Services Vault.

![2.3](screenshots/2.3.png)

---

### 2.4 Configuración de copia de seguridad

Dentro de la bóveda, en **Configuración → Propiedades**, localizamos la configuración de copia de seguridad y hacemos clic en **Actualizar**.

![2.4](screenshots/2.4.png)

---

### 2.5 Redundancia y restauración

Verificamos que:
- El tipo de replicación sea **Redundancia geográfica (GRS)**
- La restauración entre regiones esté deshabilitada

![2.5](screenshots/2.5.png)

---

### 2.6 Eliminación temporal

En la configuración de eliminación temporal, confirmamos que el período de retención sea de **14 días**.

![2.6](screenshots/2.6.png)

---

## Tarea 3 – Configurar Azure Backup para la máquina virtual

### 3.1 Habilitar copia de seguridad

Desde la bóveda, seleccionamos **Agregar copia de seguridad** y definimos el tipo de carga de trabajo y recurso.

![3.1](screenshots/3.1.png)

---

### 3.2 Crear una nueva directiva

Seleccionamos el subtipo de directiva **Estándar** y creamos una nueva política de copia de seguridad.

![3.2](screenshots/3.2.png)

---

### 3.3 Configuración de la política de backup

Definimos los siguientes valores:

- Nombre: **az104-backup**
- Frecuencia: **Diaria**
- Hora: **00:00**
- Zona horaria: local
- Retención instantánea: **2 días**

![3.3](screenshots/3.3.png)

---

### 3.4 Asociar la máquina virtual

Seleccionamos la **VM0** creada anteriormente y aplicamos la política de copia de seguridad.

![3.4](screenshots/3.4.png)

---

### 3.5 Verificación en elementos protegidos

En **Elementos protegidos → Elementos de copia de seguridad**, verificamos que la máquina virtual aparece listada.

![3.5](screenshots/3.5.png)

---

### 3.6 Estado inicial de la copia

Observamos que la VM aún no está protegida completamente porque todavía no se ha ejecutado la primera copia.

![3.6](screenshots/3.6.png)

---

### 3.7 Ejecutar copia de seguridad manual

Accedemos a los detalles de la VM y seleccionamos **Copia de seguridad ahora**.

![3.7](screenshots/3.7.png)

---

### 3.8 Estado final

Tras aproximadamente **30–60 minutos**, la copia se completa correctamente y el estado aparece como **Superado**.

![3.8](screenshots/3.8.png)

---

## Tarea 4 – Monitorizar Azure Backup

### 4.1 Crear una cuenta de almacenamiento

Accedemos a **Storage accounts** y creamos una nueva cuenta.

![4.1](screenshots/4.1.png)

---

### 4.2 Configuración de la cuenta

Seleccionamos un **nombre único**, SKU estándar y **redundancia GRS**.

![4.2](screenshots/4.2.png)

---

### 4.3 Crear la cuenta de almacenamiento

Revisamos y creamos la cuenta.

![4.3](screenshots/4.3.png)

---

### 4.4 Configuración de diagnósticos

En la Recovery Services Vault, accedemos a **Supervisión → Configuración de diagnóstico**.

![4.4](screenshots/4.4.png)

---

### 4.5 Selección de métricas y logs

Creamos una configuración llamada **Logs and Metrics to storage** y seleccionamos las métricas y registros necesarios.

![4.5](screenshots/4.5.png)

---

### 4.6 Destino de los datos

Seleccionamos **Archivar en una cuenta de almacenamiento** y elegimos la cuenta creada anteriormente.

![4.6](screenshots/4.6.png)

---

## Tarea 5 – Habilitar replicación de máquinas virtuales

### 5.1 Crear una segunda Recovery Services Vault

Creamos una nueva bóveda en un **grupo de recursos diferente**.

![5.1](screenshots/5.1.png)

---

### 5.2 Seleccionar región de destino

Seleccionamos una **región distinta** a la de la máquina virtual original.

![5.2](screenshots/5.2.png)

---

### 5.3 Configuración final y replicación

Dejamos los valores por defecto, creamos la **cuenta de automatización**, revisamos y habilitamos la replicación.

En **Objetos protegidos → Elementos replicados**, verificamos que la sincronización ha comenzado.

![5.3](screenshots/5.3.png)

---

## Limpieza de recursos

Al finalizar el laboratorio, eliminamos los grupos de recursos creados para evitar costes innecesarios.

---

## Conclusión

Este laboratorio demuestra cómo implementar:
- Copias de seguridad a nivel de VM
- Monitorización de Azure Backup
- Recuperación ante desastres entre regiones

Conceptos clave para escenarios reales de **continuidad de negocio** y para el examen **AZ-104**.
