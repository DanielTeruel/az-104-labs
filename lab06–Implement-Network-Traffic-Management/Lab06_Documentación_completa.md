# Lab 06 – Implement Network Traffic Management (AZ-104)

## Overview

En este laboratorio se implementa y prueba la **gestión del tráfico de red en Azure**, utilizando un **Azure Load Balancer (capa 4)** y un **Azure Application Gateway (capa 7)**.  
Se despliega una infraestructura base mediante **plantillas ARM**, se balancea tráfico HTTP entre máquinas virtuales y se configuran **reglas basadas en rutas** para distribuir imágenes y vídeos desde distintos servidores.

---

## Business Scenario

La organización dispone de un sitio web público y necesita distribuir el tráfico entrante entre varias máquinas virtuales para mejorar la disponibilidad y escalabilidad.  
Además, requiere enrutar distintos tipos de contenido (imágenes y vídeos) hacia servidores específicos, utilizando capacidades avanzadas de balanceo a nivel de aplicación.

---

## Task 1 – Deploy Infrastructure Using a Custom Template

Nos dirigimos al **Azure Portal → Implementar una plantilla personalizada** y seleccionamos **Crear una plantilla propia en el editor**.

![Custom Template](screenshots/1.1.png)

En el editor, pegamos el código de la plantilla ARM proporcionada.

![Paste Template](screenshots/1.2.png)

A continuación, cargamos el archivo de parámetros correspondiente.

![Paste Parameters](screenshots/1.3.png)

Una vez cargados la plantilla y los parámetros, revisamos que los detalles sean correctos, seleccionamos el **grupo de recursos** y configuramos una **contraseña de administrador** para acceder a las máquinas virtuales.

![Review Parameters](screenshots/1.4.png)

Al validar el despliegue, en mi caso se produjo un error debido a **limitaciones de la suscripción**, ya que la **IIS Custom Script Extension** no estaba permitida en la región seleccionada.  
Por este motivo, se eliminó la instalación automática de IIS desde la plantilla y se decidió instalar IIS manualmente más adelante.

![Deployment Error](screenshots/1.5.png)

Una vez eliminada la instalación de IIS de la plantilla, la validación se completa correctamente.

![Validation Successful](screenshots/1.6.png)

Posteriormente, instalamos el servicio IIS manualmente desde **Cloud Shell (PowerShell)** usando un script personalizado.

> Nota: La numeración correcta de las capturas para este paso es **2.11 y 2.12** debido a un error previo.

![Install IIS Script](screenshots/2.11.png)  
![IIS Installed](screenshots/2.12.png)

---

## Task 2 – Configure an Azure Load Balancer

Creamos un **Azure Load Balancer** desde el portal, seleccionando el grupo de recursos, **SKU Standard**, tipo **Público** y ámbito **Regional**.

![Create Load Balancer](screenshots/2.1.png)

Agregamos una **IP pública** para el frontend del Load Balancer, llamada **az104-fe**, con una IP pública denominada **az104-lbpip**.

![Frontend IP](screenshots/2.2.png)

Una vez configurada, verificamos que el frontend tiene correctamente asignada la IP pública.

![Frontend Configured](screenshots/2.3.png)

A continuación, configuramos el **backend pool**, llamado **az104-be**, seleccionando la **VNet creada en el Task 1** y agregando las máquinas virtuales desplegadas previamente.

![Backend Pool](screenshots/2.4.png)  
![Backend Details](screenshots/2.5.png)  
![Backend VMs](screenshots/2.6.png)

Revisamos la configuración y creamos el Load Balancer.

![Create Load Balancer](screenshots/2.7.png)

Una vez creado, accedemos al recurso y añadimos una **regla de equilibrio de carga**.

![Add Rule](screenshots/2.8.png)

Configuramos la regla **az104-lbrule**, utilizando el frontend y backend creados, protocolo **TCP**, puerto **80**, y un **sondeo de estado** llamado **az104-hp**, con protocolo TCP, puerto 80 y un intervalo de 5 segundos.  
Desactivamos la persistencia de sesión.

![Load Balancing Rule](screenshots/2.9.png)

Configuramos las **reglas de salida (SNAT)** para permitir acceso a Internet a los miembros del backend y guardamos los cambios.

![Outbound Rules](screenshots/2.10.png)

Al acceder a la IP pública desde el navegador, se muestra la página web con el mensaje:

`Hello World from <nombre de la VM>`

![Hello World](screenshots/2.13.png)

Si ejecutamos múltiples peticiones consecutivas, podemos observar que la respuesta alterna entre ambas máquinas virtuales, confirmando que el tráfico se distribuye correctamente.

![Load Balancing Test](screenshots/2.14.png)

---

## Task 3 – Configure an Azure Application Gateway

Accedemos a **Virtual Networks** y, dentro de la VNet creada anteriormente, revisamos las subredes existentes.

![Existing Subnets](screenshots/3.1.png)

Agregamos una nueva subred llamada **subnet-appgw** con los siguientes valores:

- Address range: **10.60.3.224/27**

![Create AppGW Subnet](screenshots/3.2.png)

Creamos un **Application Gateway** con nombre **az104-appgw**, SKU **Standard V2**, **2 instancias**, sin HTTP/2, IPv4, y seleccionamos la VNet y la subred **subnet-appgw**.

![Create Application Gateway](screenshots/3.4.png)

En el frontend, creamos una **IP pública estándar y estática**.

![Frontend IP AppGW](screenshots/3.5.png)

Agregamos ambas máquinas virtuales al backend pool principal **az104-appgwbe**.

![Backend Pool](screenshots/3.6.png)

Creamos un backend pool específico para **vídeos** llamado **az104-videobe**, asignando la **VM1**.

![Video Backend](screenshots/3.7.png)

Creamos otro backend pool para **imágenes**, asignando la **VM0**.

![Image Backend](screenshots/3.8.png)

Configuramos la regla **az104-gwrule** y el listener **az104-listener**, escuchando HTTP en el puerto 80.

![Listener Configuration](screenshots/3.9.png)

Configuramos los destinos del backend y creamos una nueva configuración HTTP llamada **az104-http**.

![Backend Settings](screenshots/3.10.png)

Agregamos reglas basadas en rutas:

- `/image/*` → **az104-imagebe**
- `/video/*` → **az104-videobe**

![Path-Based Rules](screenshots/3.11.png)  
![Image Rule](screenshots/3.12.png)  
![Video Rule](screenshots/3.13.png)

Verificamos que las reglas están correctamente configuradas.

![Rules Overview](screenshots/3.14.png)

Revisamos la configuración completa del frontend, backend y reglas.

![Final Review](screenshots/3.15.png)

Creamos el Application Gateway.

![Create AppGW](screenshots/3.16.png)

Una vez desplegado, en **Supervisión → Estado del backend**, verificamos que todos los servidores responden correctamente con código **200**.

![Backend Health](screenshots/3.17.png)

Al acceder a:

- `http://<IP-publica>/image` → se muestra la **VM0**
- `http://<IP-publica>/video` → se muestra la **VM1**

![Image Test](screenshots/3.19.png)  
![Video Test](screenshots/3.20.png)

Esto confirma que el **Application Gateway funciona correctamente con enrutamiento basado en rutas**.

---

## Resource Cleanup

Como siempre, al finalizar el laboratorio eliminamos todos los recursos para evitar costes innecesarios.

![Delete Resources](screenshots/3.21.png)
