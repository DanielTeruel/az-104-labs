# Lab 09a - Implementación de Web Apps

## Introducción
En este laboratorio, exploramos la implementación y gestión de **Azure Web Apps**, incluyendo la creación de **deployment slots**, despliegue continuo desde **GitHub** y configuración de **escalado automático**.  
Se cubren conceptos clave como: Plan de App Service, espacios de implementación, intercambio entre slots, escalado automático basado en métricas y pruebas de carga con **Azure Load Testing**.

---

## Escenario Empresarial
La organización desea migrar sus sitios web desde un **centro de datos local (on-premises)** a Azure, evitando costes de hardware y obteniendo escalabilidad y disponibilidad.  
Actualmente los sitios están en servidores Windows con PHP, y el hardware se encuentra próximo a su fin de vida útil.  
Se requiere un entorno en la nube flexible que permita probar actualizaciones en un slot de **staging** antes de desplegar en **producción**, y que pueda **escalar automáticamente** ante picos de demanda.

---

## Objetivos del Laboratorio
- Crear y configurar un **App Service** en Azure para ejecutar aplicaciones web en PHP.  
- Configurar **deployment slots** para staging y producción, permitiendo pruebas previas antes del despliegue en producción.  
- Implementar despliegue continuo desde un **repositorio GitHub externo**.  
- Realizar un **swap** entre los slots de staging y producción.  
- Configurar y probar **escalado automático** del App Service según la demanda.  
- Validar el funcionamiento de la aplicación mediante **Azure Load Testing**.  
- Limpiar los recursos creados al finalizar el laboratorio.

---

## 1. Creación del App Service y configuración inicial

Para iniciar el laboratorio, creé un **grupo de recursos** donde alojar todos los servicios necesarios.

![Creación del grupo de recursos](screenshots/1.1.png)

### Creación del App Service

Desde la página principal del portal de Azure busqué **App Services** y procedí a crear uno nuevo.  
Configuré el App Service con los siguientes parámetros:

- Nombre único: `az104lab09`
- Publicación: **Código**
- Pila de ejecución: **PHP 8.2**
- Sistema operativo: **Linux**
- Plan de precios inicial: **Básico B1**

![Configuración del App Service](screenshots/1.2.png)

Tras revisar la configuración, validé los ajustes y lancé la creación de la aplicación web.

![App Service creado correctamente](screenshots/1.3.png)

---

## 2. Configuración de Deployment Slots

Una vez finalizado el despliegue, comprobé que el App Service se había creado correctamente y estaba operativo.

![Error al añadir slot en B1](screenshots/1.4.png)

Intenté añadir un **nuevo espacio de implementación**, pero el plan **B1** no permite esta funcionalidad.  
Por ello, actualicé el **Plan de App Service** a **Premium V3 – P0V3**, lo que habilita la creación de espacios de implementación.
### Actualización del plan de App Service

![Añadir slot de staging](screenshots/1.5.png)

### Creación del slot de staging

Una vez actualizada la SKU, accedí a:

`App Service → Implementación → Espacios de implementación`

Añadí un nuevo slot con las siguientes características:

- Nombre del slot: **staging**
- Clonar configuración: **No clonar configuración**

![Verificación de los slots](screenshots/2.1.png)

Tras la creación, verifiqué que ya disponía de **dos espacios de implementación**:

- Producción
- Staging

![Confirmación de slots creados](screenshots/2.2.png)

---

## 3. Despliegue del código desde GitHub

### Configuración del Centro de Implementación

Con los slots creados, el siguiente paso fue configurar el **despliegue automático del código**.

![Configuración Deployment Center](screenshots/3.1.png)

Dentro del slot **staging**, accedí al **Centro de implementación** y configuré los siguientes ajustes:

- Origen: **Git externo**
- Repositorio: `https://github.com/Azure-Samples/php-docs-hello-world`
- Rama: **master**

Guardé los cambios para iniciar el despliegue automático.

![Verificación de Hello World en staging](screenshots/3.2.png)

---

## 4. Intercambio entre slots (Swap)

Una vez completado el despliegue, seleccioné el **enlace predeterminado del slot de staging**.  
La página mostraba correctamente el mensaje **Hello World**, confirmando que el código se había desplegado con éxito.

![Swap entre staging y producción](screenshots/3.3.png)

Después de validar el funcionamiento en staging, regresé a la sección de **Espacios de implementación** y seleccioné la opción **Swap**.

Configuré el intercambio con los valores por defecto:

- Origen: **staging**
- Destino: **producción**

Inicié el proceso de intercambio para mover el código validado a producción.

![Confirmación en producción](screenshots/4.1.png)

Una vez finalizado el swap, accedí al **enlace predeterminado del App Service en producción**.  
El contenido que antes se mostraba en staging ahora estaba visible en producción.

![Configuración escalado automático](screenshots/4.2.png)

---

## 5. Configuración de escalado automático y pruebas de carga

Con la aplicación ya en producción, accedí al **plan de App Service → Escalar horizontalmente** y configuré el **escalado automático**, estableciendo una **ráfaga máxima de 2 instancias**.

![Creación Load Test](screenshots/5.1.png)

### Pruebas de carga con Azure Load Testing

Busqué **Azure Load Testing** desde el portal de Azure.

![Nuevo recurso de prueba de carga](screenshots/5.2.png)

Creé un nuevo recurso de prueba de carga.

![Configuración de prueba basada en URL](screenshots/5.3.png)

Configuré una **prueba basada en URL**, revisé los ajustes y lancé la creación.

![Agregar solicitud HTTP al test](screenshots/5.4.png)

Dentro del plan de pruebas, añadí una **solicitud HTTP** apuntando a la **URL del App Service en producción**.

![Ejecutar prueba de carga](screenshots/5.5.png)

Guardé la configuración y ejecuté la prueba.

![Métricas del lado del cliente](screenshots/5.6.png)

Durante la ejecución observé las métricas del **lado del cliente**, como usuarios virtuales, latencia y número de peticiones.

![Métricas del lado del servidor](screenshots/5.7.png)

También revisé las métricas del **lado del servidor**, validando el comportamiento del App Service bajo carga.

![Resumen de métricas y autoscaling](screenshots/5.8.png)

Estas métricas confirmaron que el **escalado automático** funcionaba correctamente ante un aumento de demanda.

## Limpieza de Recursos

Para evitar costes innecesarios, eliminé el **Grupo de Recursos**, lo que eliminó todos los recursos asociados al laboratorio.

### Azure Portal

- Navegar a **Grupos de recursos** → Seleccionar el grupo de recursos del laboratorio → Hacer clic en **Eliminar grupo de recursos**  
- Confirmar la eliminación

![Eliminación del Grupo de Recursos](screenshots/5.9.png)

### Azure PowerShell

```powershell
Remove-AzResourceGroup -Name az104-rg9


