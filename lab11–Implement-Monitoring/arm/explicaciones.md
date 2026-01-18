# ARM Template Explicado – Lab 11 (AZ-104)

Este documento proporciona una explicación detallada del **ARM template** utilizado en el **Lab 11 – Implement Monitoring with Azure Monitor**.

El objetivo es comprender **qué despliega el template**, **cómo funciona** y **por qué se utiliza cada recurso**, siguiendo buenas prácticas reales de administración en Azure.

---

## Visión general

Este ARM template despliega un entorno completo de pruebas compuesto por:

- Una máquina virtual con Windows Server
- Componentes de red (VNet, subnet, NSG, IP pública y NIC)
- Una cuenta de almacenamiento utilizada para **Boot Diagnostics**

El template está diseñado para soportar escenarios de **monitorización con Azure Monitor y VM Insights**.

---

## Parámetros

Los parámetros permiten personalizar el despliegue sin necesidad de modificar el código del template.

### Credenciales de administrador

- **adminUsername**  
  Define el nombre de usuario del administrador local de la máquina virtual.

- **adminPassword**  
  Parámetro seguro que almacena la contraseña del administrador.

### Configuración de la máquina virtual

- **vmNamePrefix**  
  Prefijo utilizado para generar el nombre de la máquina virtual.

- **vmSize**  
  Define el tamaño de la máquina virtual (por defecto: `Standard_D2s_v3`).

- **imagePublisher**, **imageOffer**, **imageSKU**  
  Definen la imagen de Windows Server utilizada para desplegar la VM.

---

## Configuración de red

### Red virtual y subred

El template crea una red virtual con la siguiente configuración:

- Espacio de direcciones: `10.0.0.0/24`
- Subred: `10.0.0.0/26`

Esto proporciona una topología de red básica pero realista para entornos de laboratorio y pruebas.

### Grupo de seguridad de red (NSG)

Se crea un **Network Security Group** con una regla de entrada que permite:

- Tráfico TCP en el puerto **3389 (RDP)**

Esto permite el acceso remoto a la máquina virtual para tareas de administración y validación.

---

## Uso de bucles (copy)

El template utiliza la propiedad `copy` para desplegar recursos de forma dinámica:

- Interfaces de red (NIC)
- Direcciones IP públicas
- Máquinas virtuales

Aunque en este lab solo se despliega **una instancia**, esta estructura permite escalar fácilmente el entorno modificando un único valor.

---

## Cuenta de almacenamiento

Se despliega una cuenta de almacenamiento que se utiliza para:

- **Boot Diagnostics** de la máquina virtual

El nombre de la cuenta se genera dinámicamente utilizando:

- `uniqueString(subscription().subscriptionId)`

Esto garantiza que el nombre sea único a nivel global.

---

## Despliegue de la máquina virtual

La configuración de la máquina virtual incluye:

- Windows Server 2019 Datacenter
- Disco del sistema gestionado
- Asociación con la interfaz de red correspondiente
- **Boot Diagnostics habilitado**

Esta configuración representa una máquina virtual típica lista para entornos de producción.

---

## Dependencias entre recursos

El template utiliza `dependsOn` para asegurar el orden correcto de despliegue, por ejemplo:

- La máquina virtual depende de la NIC y de la cuenta de almacenamiento
- La NIC depende de la VNet, el NSG y la IP pública

Esto evita errores y condiciones de carrera durante el despliegue.

---

## Por qué se utiliza este template en el Lab 11

Este template proporciona un entorno realista para:

- Habilitar **VM Insights**
- Generar métricas y logs de monitorización
- Probar reglas de alertas (como el borrado de una VM)
- Analizar datos mediante **Azure Monitor Logs y KQL**

Sirve como base para todos los escenarios de monitorización implementados en este laboratorio.

---
