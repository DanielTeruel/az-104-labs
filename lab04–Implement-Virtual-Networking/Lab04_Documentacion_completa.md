# Lab 04 – Implement Virtual Networking (AZ-104)

## Descripción
En este laboratorio se implementa una arquitectura básica de **redes virtuales en Azure (VNets)** —no VLANs—, incluyendo la creación de VNets y subredes, el despliegue mediante **plantillas ARM**, la configuración de **Network Security Groups (NSG)** y **Application Security Groups (ASG)**, y la creación de **zonas DNS públicas y privadas**.

> Nota: Si en algún momento aparece el término “VLAN”, en realidad se refiere a **VNet (Virtual Network)**, que es el concepto correcto en Azure.

---

## Índice
- Task 1: Create a virtual network with subnets using the portal
- Task 2: Create a virtual network and subnets using a template
- Task 3: Create and configure communication between an ASG and an NSG
- Task 4: Configure public and private Azure DNS zones

---

# Task 1: Create a virtual network with subnets using the portal

### Objetivo
Crear la red **CoreServicesVnet** y sus subredes desde el portal de Azure.

### Paso 1 — Crear la VNet
Primero creamos la **VNet** en el grupo de recursos y región que deseemos.

![1.1](screenshots/1.1.png)

### Paso 2 — Configurar direccionamiento y subredes
Ajustamos los parámetros de la VNet con el espacio de direcciones **10.20.0.0/16** y creamos dos subredes:
- **DatabaseSubnet** → 10.20.20.0/24
- **SharedServicesSubnet** → 10.20.10.0/24

![1.2](screenshots/1.2.png)

### Paso 3 — Revisar y crear
Revisamos la configuración y creamos el recurso. Una vez desplegado, entramos en la VNet y verificamos que tanto la VNet como las subredes se han creado correctamente.

![1.3](screenshots/1.3.png)

### Paso 4 — Exportar plantilla ARM
Dentro de la VNet, vamos a **Automatización → Exportar plantilla** y en el apartado **Descargar** obtenemos un ZIP con los archivos **template.json** y **parameters.json**.

![1.4](screenshots/1.4.png)

---

# Task 2: Create a virtual network and subnets using a template

### Objetivo
Crear la VNet **ManufacturingVnet** reutilizando la plantilla ARM exportada.

### Paso 1 — Modificar la plantilla y parámetros
Abrimos los archivos en **Visual Studio Code** y realizamos los siguientes cambios:

**Cambios para la VNet ManufacturingVnet**
- Reemplazar `CoreServicesVnet` por `ManufacturingVnet`.
- Reemplazar `10.20.0.0` por `10.30.0.0`.

**Cambios para las subredes**
- `SharedServicesSubnet` → `SensorSubnet1`
- `10.20.10.0/24` → `10.30.20.0/24`
- `DatabaseSubnet` → `SensorSubnet2`
- `10.20.20.0/24` → `10.30.21.0/24`

**Cambios en parameters.json**
- Reemplazar la ocurrencia de `CoreServicesVnet` por `ManufacturingVnet`.

Guardamos los cambios y comprobamos que todo sea coherente con el diagrama de arquitectura.

> Los archivos finales quedan en el repositorio como `template.json` y `parameters.json`.

### Paso 2 — Desplegar la plantilla en Azure
En el portal de Azure buscamos **Implementar una plantilla personalizada** y cargamos:
- El archivo **template.json**.

![2.3](screenshots/2.3.png)

- El archivo **parameters.json**.

![2.4](screenshots/2.4.png)

Seleccionamos el grupo de recursos y el nombre que tendrá la VNet.

![2.5](screenshots/2.5.png)

Lanzamos el despliegue y, cuando termina, verificamos en **Virtual Networks** que la nueva VNet se ha creado correctamente desde JSON.

![2.6](screenshots/2.6.png)

También comprobamos que las subredes se han creado correctamente.

![2.7](screenshots/2.7.png)

---

# Task 3: Create and configure communication between an ASG and an NSG

### Objetivo
Crear un **ASG** y un **NSG**, asociar el NSG a una subred y configurar reglas de entrada y salida.

### Paso 1 — Crear el ASG
Vamos a **Application Security Groups** y creamos un nuevo grupo.

![3.1](screenshots/3.1.png)

Seleccionamos grupo de recursos, nombre y región y lo creamos.

![3.2](screenshots/3.2.png)

### Paso 2 — Crear el NSG
Creamos ahora el **Network Security Group**.

![3.3](screenshots/3.3.png)

Una vez creado, entramos en él y en **Configuración → Subredes** lo asociamos a una subred.

![3.4](screenshots/3.4.png)

En este caso lo asociamos a **SharedServicesSubnet**.

![3.5](screenshots/3.5.png)

### Paso 3 — Configurar reglas del NSG
Entramos en las reglas del NSG.

![3.6](screenshots/3.6.png)

**Regla de entrada (Inbound)**:
- Origen: ASG
- Puertos origen: *
- Destino: Any
- Puertos destino: 80, 443
- Protocolo: TCP
- Acción: Allow
- Prioridad: 100

Esto permite el tráfico desde el ASG hacia la subred.

![3.7](screenshots/3.7.png)

**Regla de salida (Outbound)**:
- Origen: Any
- Destino: Internet
- Puertos: *
- Protocolo: Any
- Acción: Deny

Esto bloquea la salida a Internet.

![3.8](screenshots/3.8.png)

Comprobamos que la regla de entrada está permitiendo tráfico desde el ASG por 80/443.

![3.9](screenshots/3.9.png)

Y que la regla de salida está denegando cualquier tráfico hacia Internet.

![3.10](screenshots/3.10.png)

---

# Task 4: Configure public and private Azure DNS zones

### Objetivo
Crear una **zona DNS pública** y una **zona DNS privada**, con sus registros y vínculos a VNets.

### Paso 1 — Crear zona DNS pública
Creamos una zona DNS pública. En este caso: **contoso7.com**.

![4.1](screenshots/4.1.png)

Una vez creada, entramos en **Administración de DNS → Conjuntos de registros** y vemos los registros existentes.

![4.2](screenshots/4.2.png)

Añadimos un nuevo registro:
- Nombre: **www**
- Tipo: **A**
- IP: **10.1.1.4**

![4.3](screenshots/4.3.png)

Probamos la resolución con:

`nslookup www.contoso7.com ns1-06.azure-dns.com`

Y comprobamos que devuelve la IP correcta.

![4.4](screenshots/4.4.png)

### Paso 2 — Crear zona DNS privada
Creamos ahora una **zona DNS privada**.

![4.5](screenshots/4.5.png)

Una vez creada, añadimos un **vínculo de VNet** desde Administración de DNS.

![4.6](screenshots/4.6.png)

Vinculamos la zona DNS privada a la **ManufacturingVnet**.

![4.7](screenshots/4.7.png)

Añadimos un registro A:
- Nombre: **sensorvm**
- IP: **10.1.1.4**

![4.8](screenshots/4.8.png)

Comprobamos que el registro se ha creado correctamente.

![4.9](screenshots/4.9.png)

Y que la zona DNS privada está correctamente vinculada a la VNet.

![4.10](screenshots/4.10.png)

---

# Limpieza de recursos

No olvidamos **borrar los recursos** una vez finalizada la práctica para evitar costes innecesarios.

![4.11](screenshots/4.11.png)
