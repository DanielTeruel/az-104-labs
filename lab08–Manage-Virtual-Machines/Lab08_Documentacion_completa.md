# Lab 08 – Gestionar Máquinas Virtuales (AZ-104)

## Introducción

En este laboratorio, exploramos la implementación y gestión de Máquinas Virtuales de Azure, comparando **máquinas virtuales independientes** con **Conjuntos de Escalado de Máquinas Virtuales (VMSS)**.  
Se cubren conceptos clave como **Zonas de Disponibilidad**, **escalado vertical**, **escalado horizontal automático** y la creación de VMs usando **Portal de Azure, PowerShell y Azure CLI**.

---

## Escenario de negocio

La organización quiere evaluar diferentes enfoques para desplegar y escalar máquinas virtuales en Azure.  
Primero, se despliegan máquinas virtuales individuales a través de **Zonas de Disponibilidad** para mejorar la disponibilidad.  
Posteriormente, se implementa un **Conjunto de Escalado de Máquinas Virtuales** para escalar recursos automáticamente según la demanda de carga de trabajo.

---

## Objetivos del laboratorio

- Desplegar máquinas virtuales en Zonas de Disponibilidad  
- Escalar recursos de cómputo y almacenamiento  
- Crear y configurar un Conjunto de Escalado de VM  
- Configurar reglas de autoescalado  
- Crear máquinas virtuales usando PowerShell y Azure CLI  
- Limpiar los recursos al finalizar el laboratorio  

---

## Tarea 1 – Crear Máquinas Virtuales en Zonas de Disponibilidad

Como siempre, comienzo creando un **Grupo de Recursos** donde se agruparán todos los recursos del laboratorio.

![Crear Grupo de Recursos](screenshots/1.1.png)

Luego, voy a **Máquinas Virtuales** y hago clic en **Crear**.  
Durante la creación de la VM, selecciono el Grupo de Recursos y configuro **dos máquinas virtuales**, cada una desplegada en una **Zona de Disponibilidad diferente** dentro de la misma región.

![Crear Máquinas Virtuales](screenshots/1.2.png)

Selecciono **Seguridad estándar**, la imagen **Windows Server 2025 Datacenter x64 Gen2**, desactivo **Azure Spot** para evitar interrupciones, y elijo la **SKU D2s_v3 estándar**, suficiente para este laboratorio.  
Configuro nombre de usuario y contraseña y desactivo la hibernación.

![Configuración básica VM](screenshots/1.3.png)

En la configuración de discos, desactivo el cifrado de host, selecciono un **SSD estándar de 128 GB**, habilito la eliminación del disco al borrar la VM y dejo la clave de cifrado gestionada por Azure.

![Configuración de disco](screenshots/1.4.png)

En redes, selecciono la VNet y subred, no abro puertos públicos de entrada, configuro que la IP pública se elimine con la NIC y no utilizo balanceador de carga.

![Configuración de red](screenshots/1.5.png)

Deshabilito los diagnósticos de arranque, ya que no son necesarios en este laboratorio.

![Configuración de supervisión](screenshots/1.6.png)

Reviso la configuración y creo las máquinas virtuales.

![Revisión y creación](screenshots/1.7.png)

Una vez completado el despliegue, ambas VMs son visibles, cada una desplegada en una Zona de Disponibilidad distinta.

![VMs creadas](screenshots/2.1.png)

---

## Tarea 2 – Escalar recursos de cómputo y almacenamiento

Selecciono **az104-vm1** y voy a **Disponibilidad + Escala → Tamaño**, donde actualizo la SKU de **D2s_v3** a **D2ds_v4**.

![Redimensionar VM](screenshots/2.2.png)

La operación se completa correctamente, confirmado por notificaciones del portal.

![Notificación redimensionamiento](screenshots/2.3.png)

Luego agrego un **disco de datos** desde **Configuración → Discos**, creando un disco **HDD de 32 GB**.

![Agregar disco de datos](screenshots/2.4.png)

El disco se adjunta correctamente a la VM.

![Disco adjuntado](screenshots/2.5.png)

Después, desacoplo el disco usando el icono de desconexión para poder modificarlo y reutilizarlo.

![Desacoplar disco](screenshots/2.6.png)

Desde la página principal del portal, selecciono el recurso de disco.

![Seleccionar disco](screenshots/2.7.png)

Actualizo el tipo de disco de **HDD** a **SSD estándar** y aplico los cambios.

![Actualizar disco](screenshots/2.8.png)

Finalmente, vuelvo a adjuntar el disco existente a la máquina virtual.

![Adjuntar disco existente](screenshots/2.9.png)

---

## Tarea 3 – Crear y configurar un Conjunto de Escalado de Máquinas Virtuales

Busco **Conjuntos de Escalado de Máquinas Virtuales** y hago clic en **Crear**.

![Crear VMSS](screenshots/3.1.png)

Configuro Grupo de Recursos, nombre del conjunto de escalado, región, Zonas de Disponibilidad, modo de orquestación **Uniforme**, seguridad estándar y capacidad manual.

![Configuración básica VMSS](screenshots/3.2.png)

Establezco un recuento base de instancias **2**, selecciono **Windows Server 2025 Datacenter Gen2**, SKU **D2s_v3**, deshabilito hibernación y configuro credenciales.

![Configuración de instancias](screenshots/3.3.png)

Creo una nueva VNet llamada **vmss-vnet8** con rango de direcciones **10.81.0.0/20** y subred **10.81.0.0/24**.

![Configuración de red](screenshots/3.4.png)

Creo un **NSG** con una regla que permite **tráfico HTTP** desde cualquier origen, para pruebas del Load Balancer.

![Regla NSG](screenshots/3.5.png)

Configuro la NIC, habilito IP pública y asocio la VNet, subred y NSG.

![Configuración NIC](screenshots/3.6.png)

Configuro un **Load Balancer público**, lo creo y lo selecciono para el conjunto de escalado.

![Load Balancer](screenshots/3.7.png)

Deshabilito diagnósticos de arranque y dejo las demás configuraciones por defecto. Después de revisar, creo el conjunto de escalado.

![Crear VMSS](screenshots/3.9.png)

Una vez desplegado, confirmo que el VMSS se creó correctamente.

![VMSS creado](screenshots/3.10.png)

---

## Tarea 4 – Configurar autoescalado del VMSS

Dentro del VMSS, en **Disponibilidad + Escala → Escalado**, cambio el modo de escalado a **Automático**.

Configuro una regla para **aumentar instancias** cuando el uso promedio de CPU supere **70% durante 10 minutos**, aumentando instancias en **50%** con un **cooldown de 5 minutos**.

![Regla de aumento](screenshots/4.1.png)

Configuro otra regla para **reducir instancias** cuando CPU baje de **30% durante 10 minutos**, reduciendo instancias en **50%**.

![Regla de reducción](screenshots/4.2.png)

Configuro límites de instancias para evitar escalado descontrolado.

![Límites de instancias](screenshots/4.3.png)

Establezco el número máximo de instancias en **5**, suficiente para el laboratorio.

![Máximo de instancias](screenshots/4.6.png)

En nuevas suscripciones de Azure, puede aparecer un error indicando que **Microsoft.Insights** no está registrado.  
Para resolverlo, ejecuto:

```bash
az provider register --namespace Microsoft.Insights
````

Verifico el estado de registro:

```bash
az provider show --namespace Microsoft.Insights --query "registrationState"
```

---

## Tarea 5 – Crear una VM usando PowerShell

Desde Cloud Shell (PowerShell):

```powershell
Get-AzVM
```

Creo una nueva máquina virtual:

```powershell
New-AzVM `
-ResourceGroupName 'az104-rg8' `
-Name 'myPSVM' `
-Location 'East US' `
-Image 'Win2019Datacenter' `
-Zone '1' `
-Size 'Standard_D2s_v3' `
-Credential (Get-Credential)
```

![VM PowerShell](screenshots/5.2.png)

Detengo la VM:

```powershell
Stop-AzVM -ResourceGroupName 'az104-rg8' -Name 'myPSVM'
```

Confirmo que el estado es **Deallocated**.

![VM detenida](screenshots/5.3.png)

Los cambios se reflejan también en el **Azure Portal**.

![Portal VM](screenshots/5.4.png)

---

## Tarea 6 – Crear una VM usando Azure CLI

Desde Cloud Shell (Bash):

```bash
az vm create \
--name myCLIVM \
--resource-group az104-rg8 \
--image Ubuntu2204 \
--admin-username localadmin \
--generate-ssh-keys
```

![VM CLI](screenshots/6.1.png)

Verifico el estado de la VM:

```bash
az vm show --name myCLIVM --resource-group az104-rg8 --show-details --output table
```

![Estado VM CLI](screenshots/6.3.png)

Desaloco la VM:

```bash
az vm deallocate --resource-group az104-rg8 --name myCLIVM
```

Los cambios se reflejan correctamente en el portal.

![Portal VM CLI](screenshots/6.5.png)

---

## Limpieza de recursos

Para evitar costes innecesarios, elimino el Grupo de Recursos, lo que borra todos los recursos asociados.

Azure Portal:

Grupos de Recursos → Eliminar Grupo de Recursos

Azure PowerShell:

```powershell
Remove-AzResourceGroup -Name az104-rg8
```

