# Lab 08 – Gestionar Máquinas Virtuales (AZ-104)

## Introducción

En este laboratorio exploramos la **implementación y gestión de máquinas virtuales Azure**, comparando **máquinas virtuales individuales** con **conjuntos de escalado de máquinas virtuales (VMSS)**.  
Se cubren conceptos como **Zonas de Disponibilidad**, **escalado vertical**, **escalado horizontal automático** y la creación de VMs usando **Portal de Azure, PowerShell y CLI de Azure**.

---

## Escenario de Laboratorio

La organización quiere evaluar diferentes enfoques para desplegar y escalar máquinas virtuales en Azure.  
Primero, se despliegan máquinas virtuales individuales en **Zonas de Disponibilidad** para mejorar la disponibilidad.  
Luego, se implementa un **Conjunto de Escalado de Máquinas Virtuales** para escalar automáticamente los recursos según la carga de trabajo.

---

## Objetivos del Laboratorio

- Desplegar máquinas virtuales en Zonas de Disponibilidad  
- Escalar recursos de cómputo y almacenamiento  
- Crear y configurar un conjunto de escalado de máquinas virtuales  
- Configurar reglas de escalado automático  
- Crear máquinas virtuales usando PowerShell y Azure CLI  
- Limpiar los recursos al finalizar el laboratorio  

---

## Tarea 1 – Crear Máquinas Virtuales en Zonas de Disponibilidad

Como siempre, empezamos creando un **Grupo de Recursos** donde agruparemos todos los recursos del laboratorio.

Luego, navegamos a **Máquinas Virtuales** y clicamos en **Crear**.  
Durante la creación, seleccionamos el Grupo de Recursos y configuramos **dos máquinas virtuales**, cada una en una **Zona de Disponibilidad diferente** dentro de la misma región.

Seleccionamos **seguridad estándar**, la imagen **Windows Server 2025 Datacenter x64 Gen2**, desactivamos **Azure Spot**, y elegimos **D2s_v3 estándar**. Configuramos usuario y contraseña y deshabilitamos hibernación.

En el apartado de discos, desactivamos cifrado de host, seleccionamos **128 GB SSD estándar**, habilitamos la opción de eliminar disco con la VM y dejamos la clave administrada por Azure.

Para redes, seleccionamos VNet y subred, no abrimos puertos públicos, configuramos la IP pública para eliminarse con la NIC y no usamos balanceador de carga.

Deshabilitamos diagnósticos de arranque, revisamos parámetros y creamos las VMs.  
Una vez creadas, podemos ver las **dos VMs**, cada una en una Zona de Disponibilidad distinta.

---

## Tarea 2 – Escalar Recursos de Cómputo y Almacenamiento

Seleccionamos **az104-vm1** y vamos a **Disponibilidad y escala → Tamaño**, donde actualizamos la SKU de **D2s_v3 a D2ds_v4**.

Luego, agregamos un **disco de datos** desde **Configuración → Discos**, creando un **HDD de 32 GB**, y lo acoplamos a la VM.  

Para desacoplarlo, usamos el icono de enchufe, y luego lo podemos asignar a otra VM con su contenido.  
Después, mejoramos el disco de **HDD a SSD estándar** y lo volvemos a acoplar a la VM1.

---

## Tarea 3 – Crear y Configurar un Conjunto de Escalado de Máquinas Virtuales

Buscamos **Conjuntos de Escalado de Máquinas Virtuales (VMSS)** y clicamos en **Crear**.  
Configuramos el Grupo de Recursos, nombre del VMSS, región, Zonas de Disponibilidad, modo de orquestación **Uniforme**, seguridad **Estándar** y capacidad manual.

Seleccionamos **2 instancias base**, imagen **Windows Server 2025 Datacenter Gen2**, SKU **D2s_v3**, deshabilitamos hibernación y configuramos credenciales.

Creamos una **VNet** llamada **vmss-vnet8** con **10.81.0.0/20** y subred **10.81.0.0/24**.  
Creamos un **NSG** con regla que permita **HTTP de cualquier origen a cualquier destino**.

Configuramos la NIC, habilitamos IP pública, asociamos la VNet, subred y NSG, y configuramos un **Load Balancer público**.

Deshabilitamos diagnósticos de arranque y dejamos el resto por defecto. Creamos el VMSS.

---

## Tarea 4 – Configurar Escalado Automático del VMSS

Dentro del VMSS, vamos a **Disponibilidad y escala → Escalado** y cambiamos a **Automático**.  

Creamos una regla para **aumentar instancias** cuando CPU promedio supere **70% por 10 min**, aumentando instancias en **50%**, con cooldown de **5 min**.

Creamos otra regla para **reducir instancias** cuando CPU promedio baje de **30% por 10 min**, reduciendo instancias en **50%**, con cooldown de **5 min**.

Configuramos los **límites de instancias**, máximo **5**, para evitar escalado descontrolado.

> Nota: Si aparece error “Microsoft.Insights no registrado”, ejecutar:

```bash
az provider register --namespace Microsoft.Insights
az provider show --namespace Microsoft.Insights --query "registrationState"
