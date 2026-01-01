## Overview
En este laboratorio, exploramos la implementación y gestión de **Azure Web Apps**, incluyendo la creación de **deployment slots**, despliegue continuo desde **GitHub** y configuración de **autoscaling**.  
Se cubren conceptos clave como: App Service Plan, deployment slots, swap entre slots, escalado automático basado en métricas y pruebas de carga con Azure Load Testing.

---

## Business Scenario
La organización desea migrar sus sitios web desde un **datacenter on-premises** a Azure, evitando costes de hardware y obteniendo escalabilidad y disponibilidad.  
Actualmente los sitios están en servidores Windows con PHP, y el hardware se encuentra próximo a su fin de vida útil.  
Se requiere un entorno en la nube flexible que permita probar actualizaciones en un slot de **staging** antes de desplegar en **producción**, y que pueda **escalar automáticamente** ante picos de demanda.

---

## Lab Objectives
- Crear y configurar un **App Service** en Azure para ejecutar aplicaciones web en PHP.  
- Configurar **deployment slots** para staging y producción, permitiendo pruebas previas antes del despliegue en producción.  
- Implementar despliegue continuo desde un **repositorio GitHub externo**.  
- Realizar un **swap** entre los slots de staging y producción.  
- Configurar y probar **autoscaling** del App Service según la demanda.  
- Validar el funcionamiento de la aplicación mediante **Azure Load Testing**.  
- Limpiar los recursos creados al finalizar el laboratorio.

---

# Lab 09a – Implementación de Azure Web Apps con Deployment Slots y Autoscaling

## 1. Creación del App Service y configuración inicial

Para iniciar el laboratorio, creé un **grupo de recursos** donde alojar todos los servicios necesarios.

### Creación del App Service

📸 **Screenshot 1.1**

Desde la página principal del portal de Azure busqué **App Services** y procedí a crear uno nuevo.  
Configuré el App Service con los siguientes parámetros:

- Nombre único: `az104lab09`
- Publicación: **Código**
- Pila de runtime: **PHP 8.2**
- Sistema operativo: **Linux**
- Plan de precios inicial: **Basic B1**

📸 **Screenshot 1.2**

Tras revisar la configuración, validé los ajustes y lancé la creación de la aplicación web.

📸 **Screenshot 1.3**

Una vez finalizado el despliegue, comprobé que el App Service se había creado correctamente y estaba operativo.

---

## 2. Configuración de Deployment Slots

### Actualización del plan de App Service

📸 **Screenshot 1.4**

Intenté añadir un **nuevo slot de implementación**, pero el plan **B1** no permite esta funcionalidad.  
Por ello, actualicé el **App Service Plan** a **Premium V3 – P0V3**, lo que habilita la creación de deployment slots.

### Creación del slot de staging

📸 **Screenshot 1.5**

Una vez actualizada la SKU, accedí a:

`App Service → Implementación → Espacios de implementación`

Añadí un nuevo slot con las siguientes características:

- Nombre del slot: **staging**
- Clonar configuración: **No clonar configuración**

📸 **Screenshot 2.1**

Tras la creación, verifiqué que ya disponía de **dos espacios de implementación**:

- Producción
- Staging

📸 **Screenshot 2.2**

Con los slots creados, el siguiente paso fue configurar el **despliegue automático del código**.

---

## 3. Despliegue del código desde GitHub

### Configuración del Deployment Center

📸 **Screenshot 3.1**

Dentro del slot **staging**, accedí al **Centro de implementación (Deployment Center)** y configuré los siguientes ajustes:

- Origen: **Git externo**
- Repositorio: `https://github.com/Azure-Samples/php-docs-hello-world`
- Rama: **master**

Guardé los cambios para iniciar el despliegue automático.

📸 **Screenshot 3.2**

Una vez completado el despliegue, seleccioné el **enlace predeterminado del slot de staging**.  
La página mostraba correctamente el mensaje **Hello World**, confirmando que el código se había desplegado con éxito.

---

## 4. Intercambio entre slots (Swap)

📸 **Screenshot 3.3**

Después de validar el funcionamiento en staging, regresé a la sección de **Espacios de implementación** y seleccioné la opción **Swap**.

Configuré el intercambio con los valores por defecto:

- Origen: **staging**
- Destino: **producción**

Inicié el proceso de swap para mover el código validado a producción.

📸 **Screenshot 4.1**

Una vez finalizado el swap, accedí al **enlace predeterminado del App Service en producción**.  
El contenido que antes se mostraba en staging ahora estaba visible en producción.

---

## 5. Configuración de autoscaling y pruebas de carga

📸 **Screenshot 4.2**

Con la aplicación ya en producción, accedí al **plan de App Service → Escalar horizontalmente** y configuré el **escalado automático**, estableciendo una **ráfaga máxima de 2 instancias**.

### Azure Load Testing

📸 **Screenshot 5.1**

Busqué **Azure Load Testing** desde el portal de Azure.

📸 **Screenshot 5.2**

Creé un nuevo recurso de prueba de carga.

📸 **Screenshot 5.3**

Configuré una **prueba basada en URL**, revisé los ajustes y lancé la creación.

📸 **Screenshot 5.4**

Dentro del plan de pruebas, añadí una **solicitud HTTP** apuntando a la **URL del App Service en producción**.

📸 **Screenshot 5.5**

Guardé la configuración y ejecuté la prueba.

📸 **Screenshot 5.6**

Durante la ejecución observé las métricas del **lado del cliente**, como usuarios virtuales, latencia y número de peticiones.

📸 **Screenshot 5.7**

También revisé las métricas del **lado del servidor**, validando el comportamiento del App Service bajo carga.

📸 **Screenshot 5.8**

Estas métricas confirmaron que el **autoscaling** funcionaba correctamente ante un aumento de demanda.
