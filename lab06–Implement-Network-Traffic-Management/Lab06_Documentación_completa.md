# Lab 06 – Implementar Gestión de Tráfico de Red (AZ-104)

## Introducción

En este laboratorio se explora la **gestión del tráfico de red en Azure**, implementando y probando dos soluciones clave:
**Azure Load Balancer (capa 4)** y **Azure Application Gateway (capa 7)**.

Se despliega una infraestructura base utilizando **plantillas ARM**, se balancea tráfico HTTP entre varias máquinas virtuales y se configuran **reglas de enrutamiento basadas en rutas** para distribuir imágenes y vídeos desde servidores específicos.

Para más información sobre la plantilla ARM utilizada en este laboratorio, revisa la carpeta [ARM Files](./arm/) que incluye:  
- [`template.json`](./arm/template.json)  
- [`parameters.json`](./arm/parameters.json)  
- [`explanations.md` (English)](./arm/explanations.md)  
- [`explicaciones.md` (Spanish)](./arm/explicaciones.md)  

## Escenario de negocio

La organización dispone de un sitio web público que recibe tráfico desde Internet.  
Es necesario distribuir las solicitudes entrantes entre varias máquinas virtuales para mejorar la **disponibilidad, resiliencia y escalabilidad**.

Además, la organización necesita servir distintos tipos de contenido:
- **Imágenes** desde un servidor específico  
- **Vídeos** desde otro servidor diferente  

Para ello, se decide implementar un **Azure Load Balancer** para balanceo básico y un **Azure Application Gateway** para balanceo avanzado a nivel de aplicación.

## Objetivos del laboratorio

- Desplegar infraestructura usando plantillas ARM  
- Configurar un Azure Load Balancer público  
- Validar el balanceo de tráfico entre máquinas virtuales  
- Configurar un Azure Application Gateway  
- Implementar reglas de enrutamiento basadas en rutas  
- Verificar el estado de los backends  
- Limpiar los recursos al finalizar el laboratorio  

---

## Tarea 1 – Desplegar la infraestructura usando una plantilla personalizada

Nos dirigimos al **Azure Portal → Implementar una plantilla personalizada** y seleccionamos **Crear una plantilla propia en el editor**.

![Implementar plantilla personalizada](screenshots/1.1.png)

En el editor, pegamos el código de la plantilla ARM proporcionada.  

### Resumen de la plantilla ARM

La plantilla ARM despliega el **entorno base del laboratorio**, que incluye:

* 2 máquinas virtuales con Windows Server 2019
* 1 red virtual (VNet) con 2 subredes
* 2 interfaces de red (NICs), una por VM
* 1 grupo de seguridad de red (NSG) con reglas básicas de RDP y HTTP

> Proporciona un entorno de prueba mínimo listo para implementar Load Balancer y Application Gateway.

> Para revisar la plantilla completa y su explicación detallada, consulta: [template.json](./arm/template.json) y [explanations.md](./arm/explanations.md).

![Plantilla ARM](screenshots/1.2.png)

A continuación, cargamos el archivo de parámetros correspondiente.  
> Archivo de parámetros: [parameters.json](./arm/parameters.json)

![Parámetros ARM](screenshots/1.3.png)

Una vez cargados la plantilla y los parámetros, revisamos que los valores sean correctos, seleccionamos el **grupo de recursos** y configuramos una **contraseña de administrador** para acceder a las máquinas virtuales.

![Revisión de parámetros](screenshots/1.4.png)

Durante la validación del despliegue, en mi caso apareció un error debido a **limitaciones de la suscripción**, ya que la **IIS Custom Script Extension** no estaba permitida en la región utilizada.  
Por este motivo, se eliminó la instalación automática de IIS desde la plantilla.

![Error de despliegue](screenshots/1.5.png)

Una vez eliminada la instalación de IIS de la plantilla, la validación se completa correctamente.

![Validación correcta](screenshots/1.6.png)

Posteriormente, instalamos manualmente el servicio IIS desde **Cloud Shell (PowerShell)** utilizando un script personalizado.

![Instalación IIS](screenshots/2.11.png)  
![IIS instalado](screenshots/2.12.png)

---


## Tarea 2 – Configurar un Azure Load Balancer

Creamos un **Azure Load Balancer** desde el Azure Portal, seleccionando el grupo de recursos, **SKU Standard**, tipo **Público** y ámbito **Regional**.

![Crear Load Balancer](screenshots/2.1.png)

Agregamos una **IP pública** para el frontend del Load Balancer, llamada **az104-fe**, y la IP pública **az104-lbpip**.

![Frontend IP](screenshots/2.2.png)

Verificamos que el frontend queda correctamente configurado con su IP pública.

![Frontend configurado](screenshots/2.3.png)

A continuación, creamos el **backend pool** llamado **az104-be**, seleccionando la **VNet creada en la Tarea 1** y agregando las máquinas virtuales desplegadas previamente.

![Backend pool](screenshots/2.4.png)  
![Detalle backend](screenshots/2.5.png)  
![VMs backend](screenshots/2.6.png)

Revisamos la configuración y creamos el Load Balancer.

![Crear Load Balancer](screenshots/2.7.png)

Una vez creado, accedemos al recurso y añadimos una **regla de equilibrio de carga**.

![Añadir regla](screenshots/2.8.png)

Configuramos la regla **az104-lbrule**, usando protocolo **TCP**, puerto **80**, el frontend y backend configurados, y un **sondeo de estado** llamado **az104-hp**, con intervalo de 5 segundos.  
Desactivamos la persistencia de sesión.

![Regla de balanceo](screenshots/2.9.png)

Seleccionamos las **reglas de salida (SNAT)** para permitir acceso a Internet a los miembros del backend.

![Reglas de salida](screenshots/2.10.png)

Al acceder a la IP pública desde el navegador, se muestra la página web con el mensaje:

`Hello World from <nombre de la VM>`

![Hello World](screenshots/2.13.png)

Al realizar múltiples peticiones, se observa que la respuesta alterna entre ambas máquinas virtuales, confirmando que el balanceo funciona correctamente.

![Prueba Load Balancer](screenshots/2.14.png)

---

## Tarea 3 – Configurar un Azure Application Gateway

Accedemos a **Virtual Networks** y revisamos la VNet creada anteriormente junto con sus subredes existentes.

![Subredes existentes](screenshots/3.1.png)

Agregamos una nueva subred llamada **subnet-appgw** con el rango **10.60.3.224/27**.

![Crear subnet AppGW](screenshots/3.2.png)

Creamos un **Application Gateway** llamado **az104-appgw**, con SKU **Standard V2**, **2 instancias**, sin HTTP/2, IPv4, y seleccionamos la VNet y la subred **subnet-appgw**.

![Crear Application Gateway](screenshots/3.4.png)

Configuramos una **IP pública estándar y estática** para el frontend.

![Frontend AppGW](screenshots/3.5.png)

Agregamos ambas máquinas virtuales al backend pool principal **az104-appgwbe**.

![Backend principal](screenshots/3.6.png)

Creamos un backend pool específico para **vídeos** llamado **az104-videobe**, asignando la **VM1**.

![Backend vídeos](screenshots/3.7.png)

Creamos otro backend pool para **imágenes**, asignando la **VM0**.

![Backend imágenes](screenshots/3.8.png)

Configuramos la regla **az104-gwrule** y el listener **az104-listener**, escuchando HTTP en el puerto 80.

![Listener](screenshots/3.9.png)

Configuramos la configuración HTTP del backend llamada **az104-http**.

![Configuración HTTP](screenshots/3.10.png)

En el apartado de abajo del todo agregamos reglas basadas en rutas:

![Reglas por ruta](screenshots/3.11.png) 

- `/image/*` → **az104-imagebe**

![Ruta imágenes](screenshots/3.12.png)  

- `/video/*` → **az104-videobe**
  
![Ruta vídeos](screenshots/3.13.png)

Verificamos que las reglas estén correctamente configuradas.

![Resumen reglas](screenshots/3.14.png)

Revisamos toda la configuración del Application Gateway y procedemos a su creación.

![Revisión final](screenshots/3.15.png)  
![Crear AppGW](screenshots/3.16.png)

Una vez desplegado, en **Supervisión → Estado del backend**, comprobamos que todos los backends responden correctamente con código **200**.

![Estado backend](screenshots/3.17.png)

Al acceder a:
- `http://<IP-publica>/image` → se muestra la **VM0**
  
![Prueba imágenes](screenshots/3.19.png)
  
- `http://<IP-publica>/video` → se muestra la **VM1**
  
![Prueba vídeos](screenshots/3.20.png)

Esto confirma que el **Application Gateway funciona correctamente con enrutamiento basado en rutas**.

---

## Limpieza de recursos

Para evitar costes innecesarios, eliminamos todos los recursos creados durante el laboratorio borrando el **grupo de recursos**.

![Eliminar recursos](screenshots/3.21.png)

O usando PowerShell:

```powershell
Remove-AzResourceGroup -Name az104-rg6
```

