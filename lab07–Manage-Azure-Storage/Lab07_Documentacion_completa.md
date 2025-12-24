# Lab 07 – Gestionar Azure Storage (AZ-104)

## Resumen
En este laboratorio trabajé con **Azure Storage** para crear y configurar cuentas de almacenamiento, asegurar blobs y recursos compartidos de archivos, gestionar reglas de ciclo de vida y controlar el acceso mediante restricciones de red y mecanismos de autenticación como los tokens SAS.

El objetivo fue comprender cómo Azure Storage puede optimizar costes, proteger los datos y aplicar controles de acceso a distintos niveles.

---

## Escenario de negocio (Roleplay)

En este escenario, la organización almacena actualmente datos en servidores de archivos locales.  
La mayoría de estos datos no se acceden con frecuencia, por lo que es necesario optimizar los costes de almacenamiento.

Además, la organización quiere:
- Proteger el acceso al almacenamiento mediante restricciones de red
- Controlar el acceso mediante autenticación y autorización
- Proteger los datos utilizando almacenamiento inmutable
- Evaluar Azure Files como alternativa a los recursos compartidos locales

---

## Objetivos del laboratorio

- Crear y configurar una cuenta de almacenamiento de Azure
- Proteger el almacenamiento mediante controles de red
- Configurar reglas de administración del ciclo de vida
- Crear y proteger contenedores de blobs
- Utilizar tokens SAS para acceso controlado
- Crear y configurar Azure File Shares
- Restringir el acceso mediante redes virtuales
- Limpiar los recursos al finalizar el laboratorio

---

## Tarea 1 – Crear y configurar una cuenta de almacenamiento de Azure

Para comenzar el laboratorio, creé un **grupo de recursos** para agrupar todos los recursos utilizados en esta práctica.  
Llamé al grupo de recursos `az104-RG7`.

![Create resource group](screenshots/1.1.png)

Dentro de este grupo de recursos, creé una **cuenta de almacenamiento** con la configuración requerida.

![Create storage account](screenshots/1.2.png)

Una vez creada la cuenta de almacenamiento, navegué a **Seguridad y redes → Redes → Administrar acceso público**.

![Manage public network access](screenshots/1.3.png)

Aquí habilité el **acceso de red pública**, pero lo limité a **redes seleccionadas**, permitiendo el acceso únicamente desde mi dirección IP pública.

![Allow selected networks](screenshots/1.4.png)

A continuación, me dirigí a **Administración de datos → Administración del ciclo de vida**.

![Lifecycle management](screenshots/1.5.png)

Agregué una nueva regla de ciclo de vida llamada **movetocool**, aplicándola a todos los blobs de la cuenta de almacenamiento.

![Add lifecycle rule](screenshots/1.6.png)

Configuré la regla para que los blobs **no modificados durante más de 30 días** pasaran del nivel de acceso **Hot** al nivel **Cool**, optimizando así los costes de almacenamiento.

![Move blobs to cool tier](screenshots/1.7.png)

---

## Tarea 2 – Crear y configurar almacenamiento seguro de blobs

Volví a la cuenta de almacenamiento y creé un **contenedor de blobs** llamado `data`, asegurándome de que el **nivel de acceso público** estuviera configurado como **Privado**.

![Create blob container](screenshots/2.1.png)

Una vez creado el contenedor, accedí a **Configuración → Directiva de acceso** y configuré el **almacenamiento de blobs inmutable** para retener los blobs durante **180 días**.

![Immutable blob policy](screenshots/2.2.png)

A continuación, cargué un archivo PNG en el contenedor, dentro de una carpeta llamada `securitytest`, utilizando el nivel de acceso **Hot**.  
Durante la carga, pude comprobar que la directiva inmutable se aplicaba correctamente.

![Upload blob](screenshots/2.3.png)

Tras la carga, intenté acceder al archivo directamente, pero recibí un mensaje indicando que **el acceso público no está permitido**.

![Public access denied](screenshots/2.4.png)

Esto ocurre porque el contenedor es privado y requiere autorización explícita.  
Para permitir un acceso limitado, generé un **token SAS** para el archivo, configurando los permisos necesarios y copiando la URL generada.

![Generate SAS token](screenshots/2.5.png)

Al abrir la URL SAS en el navegador, pude acceder al archivo correctamente, confirmando que el acceso se realiza mediante permisos SAS y reglas de red.

![Access blob with SAS](screenshots/2.6.png)

---

## Tarea 3 – Crear y configurar Azure File Storage

A continuación, me dirigí a **Recursos compartidos de archivos** dentro de la cuenta de almacenamiento.

![Navigate to file shares](screenshots/3.1.png)

Creé un nuevo recurso compartido de archivos llamado `share1`, manteniendo el nivel de acceso **Optimizado para transacciones** por defecto.

![Create file share](screenshots/3.2.png)

Desactivé la copia de seguridad para este recurso compartido, ya que no era necesaria para este laboratorio.

![Disable backup](screenshots/3.3.png)

Comprobé que el recurso compartido de archivos se había creado correctamente.

![File share created](screenshots/3.4.png)

Abrí el **Explorador de almacenamiento**, seleccioné el recurso compartido `share1` y cargué el mismo archivo PNG en él.

![Storage Browser](screenshots/3.5.png)  
![Upload file to file share](screenshots/3.6.png)

Para reforzar la seguridad, creé una **red virtual** llamada `vnet1`.

![Create virtual network](screenshots/3.7.png)

Dentro de la configuración de la red virtual, habilité un **punto de conexión de servicio** para **Microsoft.Storage** en la subred predeterminada.

![Configure service endpoint](screenshots/3.8.png)

Después, volví a la configuración de red de la cuenta de almacenamiento, permití el acceso desde la red virtual creada y eliminé mi dirección IP pública.

![Restrict network access to VNet](screenshots/3.9.png)

Tras este cambio, al intentar acceder a los recursos de almacenamiento recibí un mensaje de **acceso de red no autorizado**, confirmando que el acceso quedó restringido únicamente a la red virtual.

---

## Limpieza

Para evitar costes innecesarios y configuraciones sobrantes, eliminé todos los recursos creados durante el laboratorio.

La forma más sencilla de realizar la limpieza es eliminar el grupo de recursos completo.

### Azure Portal
Grupo de recursos → Eliminar grupo de recursos

### Azure PowerShell
```powershell
Remove-AzResourceGroup -Name az104-RG7
