# Lab 10 – Implementar Azure Backup y Recuperación ante Desastres (AZ-104)

## Introducción

En este laboratorio se explora cómo proteger datos y garantizar continuidad de negocio en Azure, utilizando **Azure Backup** y **Azure Site Recovery**.

Se despliega una infraestructura base utilizando **plantillas ARM**, que incluye una máquina virtual, red virtual, subred, NIC, IP pública y grupo de seguridad de red (NSG).
Luego se implementan copias de seguridad a nivel de máquina virtual y se habilita replicación entre regiones para recuperación ante desastres.

Para más información sobre la plantilla ARM utilizada en este laboratorio, revisa la carpeta **ARM Files** que incluye:

* 📄 [template.json](arm/template.json) – Define la infraestructura completa de la VM, red, NIC, PIP y NSG.
* 📄 [parameters.json](arm/parameters.json) – Archivo de parámetros que separa los valores de configuración del template, como nombres de VM, tamaño, red y usuario administrador.
* 📄 [explicaciones.md](arm/explicaciones.md) – Documentación **en español**, con explicación detallada de cada recurso y el flujo de despliegue.
* 📄 [explanations.md](arm/explanations.md) – Documentación **en inglés**, adaptada del archivo español para audiencias internacionales.

---

## Escenario de negocio

La organización necesita proteger una máquina virtual crítica frente a:

* Pérdida de datos
* Eliminaciones accidentales
* Fallos regionales

Se requiere una solución que permita:

* Realizar **copias de seguridad automáticas**
* Conservar los datos durante un período definido
* Replicar la infraestructura a una **región secundaria** para recuperación ante desastres

El laboratorio refleja un escenario real de **continuidad de negocio**, donde proteger y restaurar cargas de trabajo críticas es esencial para minimizar el impacto de incidentes.

---

## Objetivos del laboratorio

* Desplegar infraestructura usando **plantillas ARM**
* Crear y configurar una **Recovery Services Vault**
* Configurar **Azure Backup** para una máquina virtual
* Crear y aplicar una **directiva de copia de seguridad**
* Monitorizar Azure Backup mediante **diagnósticos y cuentas de almacenamiento**
* Implementar replicación entre regiones con **Azure Site Recovery**
* Aplicar **buenas prácticas de limpieza de recursos**

---

## Tarea 1 – Desplegar infraestructura usando una plantilla ARM

Comencé accediendo al **Portal de Azure** y seleccionando **Implementar una plantilla personalizada**.

Para el despliegue inicial de la máquina virtual utilicé una **plantilla ARM**, lo que permite definir la infraestructura como código (*Infrastructure as Code*) y garantizar despliegues **repetibles, consistentes y controlados**.

![1.1](screenshots/1.1.png)

A continuación, seleccioné la opción **Crear tu propia plantilla en el editor** y cargué la plantilla ARM correspondiente al laboratorio.

- 📄 [template.json](arm/template.json)

El archivo `template.json` define **la estructura de la infraestructura**, incluyendo:
- Red virtual y subred
- Interfaces de red y direcciones IP públicas
- Grupo de seguridad de red (NSG)
- Máquina virtual y sus características (SKU, imagen, tamaño)
  
![1.2](screenshots/1.2.png)

Después cargué el archivo de **parámetros**, donde se especifican los valores que pueden variar entre despliegues.

- 📄 [parameters.json](arm/parameters.json)

El archivo `parameters.json` permite **separar la configuración de los valores**, facilitando la reutilización de la plantilla sin modificar su lógica.  
Aquí se definen datos como:
- Nombre de la máquina virtual
- Tamaño de la VM
- Red virtual y subred
- Usuario administrador

![1.3](screenshots/1.3.png)

Finalmente, revisé los detalles del despliegue, seleccioné una **contraseña segura** para el acceso a la máquina virtual y lancé la implementación.

![1.4](screenshots/1.4.png)

---

## Tarea 2 – Crear y configurar una Recovery Services Vault

Una vez desplegada la máquina virtual, fui a **Recovery Services vaults** y creé un nuevo almacén.

![2.1](screenshots/2.1.png)

Seleccioné el **grupo de recursos**, la **región** y el **nombre** del almacén.

![2.2](screenshots/2.2.png)

Revisé la configuración y creé la Recovery Services Vault.

![2.3](screenshots/2.3.png)

Una vez creada, entré en **Configuración → Propiedades** y localicé el apartado de **configuración de copia de seguridad**, donde hice clic en **Actualizar**.

![2.4](screenshots/2.4.png)

Verifiqué que el tipo de replicación estaba configurado como **Redundancia geográfica (GRS)** y que la restauración entre regiones estaba deshabilitada.

![2.5](screenshots/2.5.png)

Por último, comprobé que la **eliminación temporal** estaba habilitada con un período de retención de **14 días**.

![2.6](screenshots/2.6.png)

---

## Tarea 3 – Configurar Azure Backup para la máquina virtual

Desde la Recovery Services Vault, seleccioné **Agregar copia de seguridad** y definí el tipo de carga de trabajo y el tipo de recurso a proteger.

![3.1](screenshots/3.1.png)

Seleccioné el subtipo de directiva **Estándar** y creé una nueva política de copia de seguridad.

![3.2](screenshots/3.2.png)

Configuré la nueva política con los siguientes valores:

- Nombre de la política: **az104-backup**
- Frecuencia: **Diaria**
- Hora: **00:00**
- Zona horaria: zona horaria local
- Retención de instantáneas: **2 días**

![3.3](screenshots/3.3.png)

A continuación, seleccioné la máquina virtual **VM0**, creada previamente, para aplicar la directiva de copia de seguridad.

![3.4](screenshots/3.4.png)

Tras revisar la configuración, creé el recurso y verifiqué que la máquina virtual aparecía en **Elementos protegidos → Elementos de copia de seguridad**.

![3.5](screenshots/3.5.png)

Pude observar que la máquina virtual aún no estaba completamente protegida, ya que todavía no se había ejecutado la primera copia según la directiva.

![3.6](screenshots/3.6.png)

Para acelerar el proceso, accedí a los detalles de la máquina virtual y ejecuté una **copia de seguridad manual**.

![3.7](screenshots/3.7.png)

Después de aproximadamente **30 a 60 minutos**, la copia de seguridad se completó correctamente y el estado de la última copia apareció como **Superado**.

![3.8](screenshots/3.8.png)

---

## Tarea 4 – Configurar monitorización de Azure Backup

A continuación, fui a **Cuentas de almacenamiento** y creé una nueva cuenta.

![4.1](screenshots/4.1.png)

Seleccioné un **nombre único**, el nivel estándar y configuré la **redundancia geográfica (GRS)**.

![4.2](screenshots/4.2.png)

Revisé la configuración y creé la cuenta de almacenamiento.

![4.3](screenshots/4.3.png)

Después regresé a la Recovery Services Vault y accedí a **Supervisión → Configuración de diagnóstico** para agregar una nueva configuración.

![4.5](screenshots/4.5.png)

Creé una configuración llamada **Logs and Metrics to storage** y seleccioné las siguientes métricas y registros:

- Azure Backup Reporting Data  
- Addon Azure Backup Job Data  
- Addon Azure Backup Alert Data  
- Azure Site Recovery Jobs  
- Azure Site Recovery Events  

![4.6](screenshots/4.6.png)

Finalmente, configuré como destino la cuenta de almacenamiento creada anteriormente, seleccionando la opción **Archivar en una cuenta de almacenamiento**.

![4.4](screenshots/4.7.png)

---

## Tarea 5 – Habilitar replicación con Azure Site Recovery

Para finalizar, creé una nueva **Recovery Services Vault** destinada a la replicación.

![5.1](screenshots/5.1.png)

Seleccioné una **región de destino diferente** a la región original de la máquina virtual.

![5.2](screenshots/5.2.png)

En los siguientes apartados dejé los valores por defecto, creé la **cuenta de automatización** y completé la configuración.

![5.3](screenshots/5.3.png)

Una vez habilitada la replicación, accedí a **Objetos protegidos → Elementos replicados**, donde pude observar que la sincronización de la máquina virtual había comenzado correctamente.

---

## Limpieza

Para evitar costes innecesarios y configuraciones sobrantes, eliminé todos los recursos creados durante el laboratorio.

La forma más sencilla de realizar la limpieza es eliminar el grupo de recursos completo.

### Azure Portal
Grupo de recursos → Eliminar grupo de recursos

### Azure PowerShell
```powershell
Remove-AzResourceGroup -Name az104-RG10
