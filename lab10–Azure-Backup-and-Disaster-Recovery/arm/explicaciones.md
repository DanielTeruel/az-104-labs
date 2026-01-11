## Explicación de la plantilla ARM

Esta plantilla ARM despliega un **entorno de laboratorio con 1 máquina virtual (VM) en Azure** con la configuración mínima necesaria para pruebas de conectividad y administración remota. La plantilla incluye una **VNet, subred, NIC, IP pública y NSG** para que la VM quede accesible mediante RDP.

El objetivo principal de la plantilla es **proporcionar un entorno base listo para pruebas de infraestructura de Azure y gestión de máquinas virtuales**, sin agregar aplicaciones adicionales para mantenerlo sencillo y económico.

---

### 1. Parámetros

Los parámetros permiten que la plantilla sea **flexible y reutilizable**, sin necesidad de modificar el código. Se definen nombres de recursos, contraseñas, configuraciones de red y características de la VM.

* **vmNamePrefix**: Prefijo para el nombre de la máquina virtual.
* **nicNamePrefix**: Prefijo para el nombre de la interfaz de red asociada a la VM.
* **pipNamePrefix**: Prefijo para la dirección IP pública.
* **virtualNetworkName**: Nombre de la red virtual (VNet).
* **addressPrefix**: Rango de direcciones de la VNet.
* **subnet0Name / subnet0Prefix**: Nombre y rango de la subred donde se conectará la VM.
* **nsgName**: Nombre del Network Security Group (NSG) que controlará el tráfico.
* **adminUsername / adminPassword**: Credenciales de administrador para la VM.
* **imagePublisher / imageOffer / imageSKU**: Imagen del sistema operativo de la VM (Windows Server 2019 Datacenter en este caso).
* **vmSize**: Tamaño de la VM (Standard_D2s_v3 por defecto).

> Gracias a los parámetros, podemos desplegar la plantilla en diferentes grupos de recursos o regiones sin cambiar el template principal.

---

### 2. Recursos

La plantilla crea los recursos **en un orden lógico**, usando dependencias (`dependsOn`) para garantizar que cada recurso se cree correctamente antes de que otro dependa de él.

#### a) Network Security Group (NSG)

* **Tipo**: `Microsoft.Network/networkSecurityGroups`
* Contiene una regla básica de seguridad:

  * **RDP (puerto 3389)**: Permite conectarse de manera remota a Windows desde cualquier dirección IP.

> El NSG se asigna a la NIC de la VM para que las reglas de seguridad se apliquen automáticamente.

---

#### b) Virtual Network (VNet)

* **Tipo**: `Microsoft.Network/virtualNetworks`
* La plantilla crea **una VNet con rango 10.0.0.0/24**, dividida en **una subred**:

  * **subnet0**: Para la VM desplegada, con rango 10.0.0.0/26

> La VNet proporciona un espacio de red aislado y controlado para la VM.

---

#### c) Public IP Address (PIP)

* **Tipo**: `Microsoft.Network/publicIpAddresses`
* Se crea **una IP pública estática**, que permite acceder a la VM desde Internet mediante RDP.

---

#### d) Network Interface (NIC)

* **Tipo**: `Microsoft.Network/networkInterfaces`
* Se crea **una NIC**, conectada a la subred `subnet0`, asociada al NSG y a la IP pública creada.

> Esto garantiza que la VM tenga conectividad interna y externa, además de aplicar las reglas de seguridad definidas.

---

#### e) Máquina Virtual (VM)

* **Tipo**: `Microsoft.Compute/virtualMachines`
* Se crea **una VM con Windows Server 2019 Datacenter**, configurada con:

  * **HardwareProfile**: Tamaño `Standard_D2s_v3`
  * **OSProfile**: Usuario administrador (`localadmin`) y contraseña segura
  * **StorageProfile**: Disco del sistema operativo gestionado, creado a partir de la imagen de Windows Server 2019 Datacenter
  * **NetworkProfile**: Conexión a la NIC creada previamente

> La VM representa el servidor base para el laboratorio, listo para administración remota y pruebas de infraestructura.

---

### 3. Flujo de despliegue

1. **Se crea la VNet** con su subred definida.
2. **Se crea el NSG** con reglas de RDP.
3. **Se crea la IP pública** para la VM.
4. **Se crea la NIC**, asociada a la subred, NSG y PIP.
5. **Se crea la VM**, conectada a la NIC y lista para uso.

> Gracias a las dependencias (`dependsOn`), Azure garantiza que cada recurso se despliegue en el orden correcto, evitando errores de creación.

---

### 4. Notas adicionales

* La plantilla **no instala aplicaciones ni servicios adicionales**, para reducir costos y complejidad.
* Se puede **ampliar fácilmente** para agregar más VMs, subredes, reglas de NSG o balanceadores de carga según las necesidades del laboratorio.
* Es un **entorno de prueba mínimo**, suficiente para **administrar VMs, probar conectividad y configurar servicios básicos de red** en Azure.
