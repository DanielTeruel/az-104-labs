# Lab 05 – Implementar Conectividad entre Redes en Azure (AZ-104)

## Resumen
En este laboratorio trabajé con **conectividad entre redes virtuales en Azure** para permitir la comunicación entre recursos ubicados en diferentes VNets. Durante la práctica:

- Creé dos máquinas virtuales en dos redes virtuales diferentes.
- Verifiqué la conectividad inicial usando **Network Watcher**.
- Configuré **emparejamiento de redes virtuales (VNet Peering)**.
- Volví a comprobar la conectividad usando **PowerShell**.
- Creé una **ruta personalizada (UDR)** para controlar el enrutamiento del tráfico.
- Asocié la tabla de rutas a una subred específica.

Este laboratorio me permitió entender cómo se interconectan redes en Azure y cómo se puede controlar el flujo de tráfico entre subredes y VNets.

## Escenario de Negocio
La organización separa los servicios centrales (Core Services) del resto de departamentos, como el área de fabricación. Sin embargo, en algunos escenarios es necesario que ambos entornos puedan comunicarse. Para ello, se debe configurar conectividad segura entre redes virtuales separadas y controlar el enrutamiento del tráfico.

## Objetivos del Laboratorio

- Crear dos máquinas virtuales en redes virtuales diferentes.
- Verificar la conectividad entre VNets usando Network Watcher.
- Configurar emparejamiento entre redes virtuales (VNet Peering).
- Verificar la conectividad usando PowerShell.
- Crear una tabla de rutas personalizada (UDR).
- Asociar la ruta a una subred específica.
- Aplicar buenas prácticas de limpieza de recursos.

---

## Tarea 1 – Crear CoreServicesVM y su red virtual

Como siempre, comencé creando el **grupo de recursos** donde se alojarán todos los recursos del laboratorio.

A continuación, fui a **Máquinas virtuales** y creé una nueva VM con la siguiente configuración:

Configuración | Valor
--- | ---
Grupo de recursos | az104-rg5
Nombre de la máquina virtual | CoreServicesVM
Región | France Central
Opciones de disponibilidad | No se requiere redundancia de infraestructura
Tipo de seguridad | Estándar
Imagen | Windows Server 2025 Datacenter - x64 Gen2
Tamaño | Standard_DS2_v3
Usuario | localadmin
Contraseña | ********
Puertos de entrada públicos | Ninguno

![1.1](screenshots/1.1.png)

Durante la creación, creé también la red virtual donde se conectará esta VM.

![1.2](screenshots/1.2.png)

Configuré la red virtual con los siguientes parámetros:

Configuración | Valor
--- | ---
Nombre | CoreServicesVnet
Espacio de direcciones | 10.0.0.0/16
Subred | Core
Rango de subred | 10.0.0.0/24

![1.3](screenshots/1.3.png)

Después, deshabilité el **diagnóstico de arranque**.

![1.4](screenshots/1.4.png)

Finalmente, revisé la configuración y creé la máquina virtual.

![1.5](screenshots/1.5.png)

---

## Tarea 2 – Crear ManufacturingVM en otra red virtual

A continuación, creé una segunda máquina virtual en otra red virtual distinta con la siguiente configuración:

Configuración | Valor
--- | ---
Grupo de recursos | az104-rg5
Nombre de la máquina virtual | ManufacturingVM
Región | France Central
Tipo de seguridad | Estándar
Opciones de disponibilidad | No se requiere redundancia de infraestructura
Imagen | Windows Server 2025 Datacenter - x64 Gen2
Tamaño | Standard_DS2_v3
Usuario | localadmin
Contraseña | ********
Puertos de entrada públicos | Ninguno

![1.6](screenshots/1.6.png)

Durante la creación, configuré una nueva red virtual con estos parámetros:

Configuración | Valor
--- | ---
Nombre | ManufacturingVnet
Espacio de direcciones | 172.16.0.0/16
Subred | Manufacturing
Rango de subred | 172.16.0.0/24

![2.1](screenshots/2.1.png)

---

## Tarea 3 – Probar conectividad con Network Watcher

Una vez creadas ambas máquinas virtuales, fui a **Network Watcher → Solucionar problemas de conexión** y configuré una prueba:

- Origen: CoreServicesVM  
- Destino: ManufacturingVM  
- Protocolo: TCP  
- Puerto: 3389  

![2.2](screenshots/2.2.png)

Al ejecutar el diagnóstico, la prueba de conectividad resultó **Inaccesible**, ya que las VNets aún no estaban emparejadas.

![3.1](screenshots/3.1.png)

![3.2](screenshots/3.2.png)

---

## Tarea 4 – Configurar el emparejamiento entre VNets

Fui a **Redes virtuales → CoreServicesVnet → Emparejamientos** y agregué un nuevo emparejamiento.

![4.2](screenshots/4.2.png)

Configuré el emparejamiento entre **CoreServicesVnet** y **ManufacturingVnet**, permitiendo:

- Acceso entre ambas VNets
- Tráfico reenviado (tráfico que no se origina en esta VNet)

![4.3](screenshots/4.3.png)

Apliqué la configuración tanto de CoreServices hacia Manufacturing como de Manufacturing hacia CoreServices.

![4.4](screenshots/4.4.png)

Una vez creado, verifiqué que en ambas VNets el estado del emparejamiento aparecía como **Conectado**.

![4.5](screenshots/4.5.png)

![4.6](screenshots/4.6.png)

---

## Tarea 5 – Probar conectividad usando PowerShell

Ahora probé la conectividad desde **ManufacturingVM** usando **Ejecutar comando → RunPowerShellScript**.

![5.1](screenshots/5.1.png)

Ejecuté el siguiente comando:

```powershell
Test-NetConnection 10.0.0.4 -Port 3389
