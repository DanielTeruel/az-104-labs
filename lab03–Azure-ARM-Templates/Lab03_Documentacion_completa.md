# Lab 03 – Gestionar Recursos de Azure usando Plantillas de Azure Resource Manager (AZ-104)

## Resumen
El laboratorio se centró en desplegar discos administrados en Azure. Cubrí:
- Crear y exportar plantillas ARM
- Editar plantillas y redeplegar recursos
- Usar Azure Cloud Shell con PowerShell y Bash
- Desplegar recursos usando plantillas Bicep
- Verificar despliegues y configuraciones de recursos

Este ejercicio me dio experiencia práctica con prácticas de infraestructura como código (IaC), mejorando la consistencia de los despliegues y reduciendo errores humanos.

## Escenario de Negocio
Quería explorar cómo automatizar y simplificar los despliegues de recursos en Azure. Usar plantillas me ayudó a reducir la carga administrativa, minimizar errores humanos y mantener consistencia en los despliegues.

## Objetivos del Laboratorio

- Comprender cómo crear y gestionar discos administrados en Azure.
- Aprender a exportar plantillas ARM desde recursos existentes.
- Practicar la edición de plantillas ARM y redeplegar recursos.
- Desplegar recursos usando Azure Cloud Shell con PowerShell.
- Desplegar recursos usando Azure CLI (Bash) en Cloud Shell.
- Desplegar recursos usando plantillas Bicep.
- Verificar que los despliegues fueron exitosos y validar las configuraciones de los recursos.
- Obtener experiencia práctica con infraestructura como código (IaC).

---

## Tarea 1 – Crear un disco administrado y exportar una plantilla ARM

Comencé creando el grupo de recursos para el laboratorio.  

![1.1](screenshots/1.1.png)

Fui a **Discos** y creé un nuevo disco HDD estándar de 32 GB.  

![1.2](screenshots/1.2.png)

Revisé la configuración y creé el disco.  

![1.3](screenshots/1.3.png)

Una vez creado el disco, fui a la sección **Automatización → Exportar plantilla** para revisar la plantilla ARM. Hice clic en **Descargar** para guardar la configuración.  

![1.4](screenshots/1.4.png)

Se descargó un archivo ZIP que contenía `template.json` y `parameters.json` para el siguiente despliegue.  

![1.5](screenshots/1.5.png)

---

## Tarea 2 – Desplegar un nuevo disco administrado usando una plantilla personalizada

Luego fui a **Desplegar una plantilla personalizada** en el portal de Azure y subí la plantilla previamente descargada al editor.  

![2.1](screenshots/2.1.png)

Subí el archivo de plantilla a Azure y edité el valor por defecto del parámetro `disk_name` a `"az104-disk2"` y configuré el nombre del parámetro como `"disk_name"`.  

![2.2](screenshots/2.2.png)

También edité `parameters.json` para que coincidiera con el nombre del parámetro de la plantilla (`disk_name`).  

![2.3](screenshots/2.3.png)

Desplegué el recurso.  

![2.4](screenshots/2.4.png)

Verifiqué que el despliegue fue exitoso y que el nuevo disco aparecía en el portal de Azure.  

![2.5](screenshots/2.5.png)

Revisé los detalles del despliegue en **Grupo de Recursos → Implementaciones** para consultar la plantilla ARM y los parámetros actualizados.  

![2.6](screenshots/2.6.png)

![2.7](screenshots/2.7.png)

---

## Tarea 3 – Desplegar una plantilla usando Azure Cloud Shell y PowerShell

Antes de abrir PowerShell, creé una nueva cuenta de almacenamiento en Cloud Shell para guardar los archivos de plantilla que necesitaría más adelante.  

![3.1](screenshots/3.1.png)

![3.2](screenshots/3.2.png)

Una vez lista la cuenta de almacenamiento, abrí PowerShell en Cloud Shell, cambié a la vista clásica y subí `template.json` y `parameters.json`. Confirmé que los archivos eran visibles en el explorador de archivos.  

![3.3](screenshots/3.3.png)

Edité la plantilla para actualizar el valor por defecto del parámetro `disk_name` (líneas 5–6).  

![3.5](screenshots/3.5.png)

Edité `parameters.json` línea 5 para que coincidiera con los cambios en la plantilla.  

![3.6](screenshots/3.6.png)

Desplegué la plantilla usando PowerShell:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName az104-rg3 -TemplateFile template.json -TemplateParameterFile parameters.json
````

![3.7](screenshots/3.7.png)

Verifiqué que el despliegue fue exitoso.

![3.8](screenshots/3.8.png)

![3.9](screenshots/3.9.png)

---

## Tarea 4 – Desplegar la plantilla usando Azure CLI (Bash)

Luego cambié a Bash en Cloud Shell, navegué al explorador de archivos de la cuenta de almacenamiento y edité `template.json` y `parameters.json` líneas 5–6 para nombrar el disco `"disk4"`.

![4.1](screenshots/4.1.png)

Desplegué la plantilla actualizada usando:

```bash
az deployment group create --resource-group az104-rg3 --template-file template.json --parameters parameters.json
```

![4.2](screenshots/4.2.png)

Listé los discos para verificar el despliegue:

```bash
az disk list --resource-group az104-rg3 --output table
```

![4.3](screenshots/4.3.png)

![4.4](screenshots/4.4.png)

---

## Tarea 5 – Desplegar un disco administrado usando Bicep

Subí `azuredeploydisk.bicep` a Cloud Shell y edité los parámetros:

* `managedDiskName` → `"az104-disk5"` (línea 2)
* `diskSizeInGiB` → `32` (línea 7)
* `sku name` → `StandardSSD_LRS` (línea 26)

![5.2](screenshots/5.2.png)

Desplegué la plantilla Bicep:

```bash
az deployment group create --resource-group az104-rg3 --template-file azuredeploydisk.bicep
```

![5.3](screenshots/5.3.png)

Listé los discos para confirmar que todo estaba correcto:

```bash
az disk list --resource-group az104-rg3 --output table
```

![5.4](screenshots/5.4.png)

![5.5](screenshots/5.5.png)

---

# Limpieza

Para evitar costes innecesarios, eliminé el grupo de recursos, lo que eliminó todos los recursos asociados:

## Azure PowerShell

```powershell
Remove-AzResourceGroup -Name az104-rg3
```

## Azure CLI

```bash
az group delete --name az104-rg3
```

```
```
