# Lab 04 – Implementar Redes Virtuales en Azure (AZ-104)

## Resumen
En este laboratorio trabajé con **redes virtuales de Azure (VNets)** para diseñar y desplegar una arquitectura básica de red. Durante la práctica:

- Creé redes virtuales y subredes desde el portal de Azure.
- Exporté y reutilicé una plantilla ARM para desplegar una nueva VNet.
- Configuré **Grupos de Seguridad de Red (NSG)** y **Grupos de Seguridad de Aplicación (ASG)**.
- Implementé reglas de seguridad para controlar el tráfico de entrada y salida.
- Configuré **zonas DNS públicas y privadas** y verifiqué la resolución de nombres.

Este laboratorio me permitió entender mejor cómo se estructura el networking en Azure y cómo integrar seguridad y resolución de nombres dentro de una arquitectura cloud.

## Escenario de Negocio
La organización necesita una infraestructura de red segmentada para distintos entornos y servicios. Además, es necesario controlar el tráfico mediante reglas de seguridad y disponer de resolución DNS tanto pública como privada para los recursos internos y externos.

## Objetivos del Laboratorio

- Crear y configurar redes virtuales y subredes en Azure.
- Exportar una plantilla ARM desde un recurso existente.
- Modificar una plantilla ARM y desplegar una nueva red virtual.
- Crear y configurar un NSG y un ASG.
- Asociar un NSG a una subred y definir reglas de seguridad.
- Crear y configurar una zona DNS pública.
- Crear y configurar una zona DNS privada y vincularla a una VNet.
- Verificar la resolución de nombres DNS.
- Aplicar buenas prácticas de limpieza de recursos.

---

## Tarea 1 – Crear una red virtual con subredes usando el portal

Comencé creando una **red virtual (VNet)** en el grupo de recursos y la región deseada.

![1.1](screenshots/1.1.png)

Configuré el espacio de direcciones **10.20.0.0/16** y creé dos subredes:
- **DatabaseSubnet** → 10.20.20.0/24  
- **SharedServicesSubnet** → 10.20.10.0/24  

![1.2](screenshots/1.2.png)

Revisé la configuración y creé el recurso.

![1.3](screenshots/1.3.png)

Una vez creada la VNet, fui a **Automatización → Exportar plantilla** y descargué la plantilla ARM en formato ZIP, que contenía los archivos `template.json` y `parameters.json`.

![1.4](screenshots/1.4.png)

---

## Tarea 2 – Crear una nueva red virtual usando una plantilla ARM

Abrí los archivos exportados en **Visual Studio Code** y realicé los siguientes cambios:

- Cambié el nombre de la red de `CoreServicesVnet` a `ManufacturingVnet`.
- Cambié el espacio de direcciones a **10.30.0.0/16**.
- Renombré las subredes:
  - `SharedServicesSubnet` → `SensorSubnet1` (10.30.20.0/24)
  - `DatabaseSubnet` → `SensorSubnet2` (10.30.21.0/24)
- Actualicé también el archivo `parameters.json` para que coincidiera con los nuevos nombres.

Una vez listos los cambios, fui al portal de Azure y busqué **Implementar una plantilla personalizada**. Cargué el archivo `template.json`.

![2.3](screenshots/2.3.png)

Después cargué el archivo `parameters.json`.

![2.4](screenshots/2.4.png)

Seleccioné el grupo de recursos y el nombre que tendría la nueva VNet.

![2.5](screenshots/2.5.png)

## Plantillas ARM utilizadas

Las plantillas completas utilizadas en este laboratorio pueden consultarse aquí:

- 📄 [template.json](arm/template.json)
- 📄 [parameters.json](arm/parameters.json)

Estos archivos pueden descargarse y reutilizarse directamente para reproducir el laboratorio.

Inicié el despliegue y, cuando terminó, verifiqué que la nueva red virtual se había creado correctamente.

![2.6](screenshots/2.6.png)

También comprobé que las subredes se habían creado correctamente.

![2.7](screenshots/2.7.png)

---

## Tarea 3 – Crear y configurar un ASG y un NSG

Primero fui a **Grupos de seguridad de aplicaciones (ASG)** y creé un nuevo grupo.

![3.1](screenshots/3.1.png)

Seleccioné el grupo de recursos, el nombre y la región, y lo creé.

![3.2](screenshots/3.2.png)

A continuación, creé un **Grupo de seguridad de red (NSG)**.

![3.3](screenshots/3.3.png)

Una vez creado, entré en el NSG y en **Configuración → Subredes** lo asocié a una subred.

![3.4](screenshots/3.4.png)

En este caso, lo asocié a **SharedServicesSubnet**.

![3.5](screenshots/3.5.png)

Después entré en la configuración de reglas del NSG.

![3.6](screenshots/3.6.png)

Creé una **regla de entrada** que permite el tráfico desde el ASG hacia la subred por los puertos **80 y 443** usando **TCP**, con prioridad **100**.

![3.7](screenshots/3.7.png)

A continuación, creé una **regla de salida** que **deniega todo el tráfico hacia Internet**.

![3.8](screenshots/3.8.png)

Verifiqué que la regla de entrada permite correctamente el tráfico desde el ASG por los puertos 80 y 443.

![3.9](screenshots/3.9.png)

Y también confirmé que la regla de salida bloquea cualquier tráfico hacia Internet.

![3.10](screenshots/3.10.png)

---

## Tarea 4 – Configurar zonas DNS públicas y privadas

Primero creé una **zona DNS pública** llamada **contoso7.com**.

![4.1](screenshots/4.1.png)

Una vez creada, entré en la administración de registros DNS.

![4.2](screenshots/4.2.png)

Añadí un nuevo registro:
- Nombre: **www**
- Tipo: **A**
- Dirección IP: **10.1.1.4**

![4.3](screenshots/4.3.png)

Verifiqué la resolución de nombres usando:

`nslookup www.contoso7.com ns1-06.azure-dns.com`

![4.4](screenshots/4.4.png)

Después creé una **zona DNS privada**.

![4.5](screenshots/4.5.png)

Una vez creada, añadí un **vínculo de red virtual**.

![4.6](screenshots/4.6.png)

Vinculé la zona DNS privada a la **ManufacturingVnet**.

![4.7](screenshots/4.7.png)

Añadí un registro de tipo A:
- Nombre: **sensorvm**
- Dirección IP: **10.1.1.4**

![4.8](screenshots/4.8.png)

Verifiqué que el registro se había creado correctamente.

![4.9](screenshots/4.9.png)

Y confirmé que la zona DNS privada estaba correctamente vinculada a la red virtual.

![4.10](screenshots/4.10.png)

---

## Limpieza de recursos

Para evitar costes innecesarios, elimino el Grupo de Recursos, lo que borra todos los recursos asociados.

![4.11](screenshots/4.11.png)

Azure Portal:

Grupos de Recursos → Eliminar Grupo de Recursos

Azure PowerShell:

```powershell
Remove-AzResourceGroup -Name az104-rg8
```


