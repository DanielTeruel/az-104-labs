## Explicación de la plantilla ARM

Esta plantilla ARM despliega un **entorno simple de 2 máquinas virtuales (VMs) en Azure** con la configuración mínima necesaria para un laboratorio. No incluye la instalación de IIS para **reducir costos** y mantener el despliegue sencillo usando solo 2 VMs.

El objetivo principal de la plantilla es **proporcionar un entorno base listo para probar Azure Load Balancer y Application Gateway**.

---

### 1. Parámetros

Los parámetros permiten que la plantilla sea **flexible y reutilizable**, sin necesidad de modificar el código. Se definen nombres de recursos, contraseñas y configuraciones de red.

* **virtualMachines_az104_06_vm0_name / vm1_name**: Nombres de las dos máquinas virtuales.
* **virtualNetworks_az104_06_vnet1_name**: Nombre de la red virtual (VNet).
* **networkInterfaces_az104_06_nic0_name / nic1_name**: Nombres de las interfaces de red asociadas a cada VM.
* **networkSecurityGroups_az104_06_nsg1_name**: Nombre del grupo de seguridad de red (NSG).
* **adminPassword**: Contraseña segura para los administradores de las VMs.

> Gracias a los parámetros, podemos desplegar la plantilla en diferentes grupos de recursos o regiones sin cambiar el archivo principal.

---

### 2. Recursos

La plantilla crea los recursos **en un orden lógico**, usando dependencias (`dependsOn`) para garantizar que cada recurso se cree correctamente antes de que otro dependa de él.

#### a) Network Security Group (NSG)

* **Tipo**: `Microsoft.Network/networkSecurityGroups`
* Contiene reglas básicas de seguridad:

  * **RDP (puerto 3389)**: Permite conectarse de manera remota a Windows.
  * **HTTP (puerto 80)**: Permitido en caso de necesitar pruebas web posteriores.
* Cada regla tiene prioridad y dirección definida (`Inbound` o `Outbound`) para controlar el flujo de tráfico.

#### b) Virtual Network (VNet)

* **Tipo**: `Microsoft.Network/virtualNetworks`
* La plantilla crea **una VNet con rango 10.60.0.0/22**, dividida en **2 subredes**:

  * **subnet0**: Para la primera VM
  * **subnet1**: Para la segunda VM
* Permite habilitar políticas de red privada y protección de endpoints según sea necesario.
* La VNet está configurada **sin DDoS**, para simplificar el laboratorio y reducir costos.

#### c) Network Interfaces (NICs)

* **Tipo**: `Microsoft.Network/networkInterfaces`
* Se crean **2 NICs**, una por cada VM, conectadas a las subredes correspondientes.
* Cada NIC está asociada al NSG creado previamente, garantizando que las reglas de seguridad se apliquen automáticamente a la VM.

#### d) Máquinas Virtuales (VMs)

* **Tipo**: `Microsoft.Compute/virtualMachines`
* Se crean **2 máquinas virtuales con Windows Server 2019**.
* Cada VM tiene:

  * **HardwareProfile**: Tamaño `Standard_D2s_v3`
  * **StorageProfile**: Disco del sistema operativo de 127 GB, creado a partir de la imagen de Windows Server 2019 Datacenter
  * **OSProfile**: Configuración de usuario administrador (`localadmin`) y provisión del agente de Azure
  * **NetworkProfile**: Conexión a la NIC correspondiente

> Estas VMs representan los servidores que se usarán en el laboratorio para balanceo de tráfico y pruebas de enrutamiento.

---

### 3. Flujo de despliegue

1. **Se crea el NSG** con reglas de seguridad básicas.
2. **Se crea la VNet** con sus subredes.
3. **Se crean las NICs**, asociadas a subredes y al NSG.
4. **Se crean las VMs**, conectadas a sus NICs correspondientes.

> Gracias a las dependencias definidas, Azure garantiza que cada recurso se despliegue en el orden correcto, evitando errores de creación.

---

### 4. Notas adicionales

* La plantilla **no instala IIS ni aplicaciones**, para reducir la complejidad y costos del laboratorio.
* Se puede **modificar fácilmente** para agregar más VMs, subredes o reglas de NSG según se necesite.
* Es un **entorno de prueba mínimo**, suficiente para probar **Load Balancer y Application Gateway** con tráfico HTTP.

---
