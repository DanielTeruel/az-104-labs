# Lab 11 – Implementar Monitorización con Azure Monitor (AZ-104)

## Introducción

En este laboratorio se exploran las capacidades de **Azure Monitor** para supervisar recursos en Azure, crear alertas y notificar eventos críticos a los equipos de operaciones.

Se despliega una infraestructura base utilizando una **plantilla ARM**, que incluye una máquina virtual y su red asociada.  

- 📄 [template.json](/arm/template.json)

Posteriormente, se habilita **VM Insights**, se crean **reglas de alertas**, **grupos de acciones**, se prueban notificaciones reales y se configuran **reglas de procesamiento de alertas** para suprimir avisos durante ventanas de mantenimiento.

Finalmente, se utilizan **consultas KQL en Azure Monitor Logs** para analizar métricas y eventos capturados por la plataforma.

---

## Escenario de negocio

La organización ha migrado su infraestructura a Azure y necesita garantizar que los **administradores sean notificados ante cambios críticos**, como la eliminación de máquinas virtuales.

Para ello, se requiere una solución que permita:

- Supervisar máquinas virtuales en tiempo real
- Detectar eventos importantes a nivel de suscripción
- Enviar notificaciones automáticas al equipo de operaciones
- Suprimir alertas durante períodos de mantenimiento planificado
- Analizar métricas y eventos mediante consultas centralizadas

Este laboratorio representa un escenario real de **monitorización proactiva**, esencial para mantener la estabilidad y seguridad de los entornos en producción.

---

## Objetivos del laboratorio

- Desplegar infraestructura usando **plantillas ARM**
- Configurar **Azure Monitor** y **VM Insights**
- Crear reglas de alertas basadas en eventos
- Configurar **grupos de acciones** con notificaciones por correo
- Probar alertas eliminando una máquina virtual
- Crear reglas de procesamiento de alertas para mantenimiento
- Consultar métricas y eventos usando **Azure Monitor Logs (KQL)**

---

## Tarea 1 – Desplegar infraestructura y habilitar Azure Monitor

Comencé accediendo al **Portal de Azure** y seleccionando **Implementar una plantilla personalizada**.

![1.1](screenshots/1.1.png)

A continuación, creé una plantilla personalizada utilizando el **editor** e implementé la plantilla ARM correspondiente al laboratorio.

- 📄 [template.json](lab11–Implement-Monitoring/arm/template.json)

![1.2](screenshots/1.2.png)

Durante el despliegue, introduje el **usuario y contraseña** en el apartado **Detalles de la instancia**, necesarios para la creación de la máquina virtual.

![1.3](screenshots/1.3.png)

Una vez completada la implementación, verifiqué que los **recursos se habían desplegado correctamente** dentro del grupo de recursos.

![1.4](screenshots/1.4.png)

Después, accedí al servicio **Azure Monitor**.

![1.5](screenshots/1.5.png)

Desde Azure Monitor, accedí a la información de la máquina virtual y a la configuración de **Insights**.

![1.6](screenshots/1.6.png)

En el apartado de información general, comprobé que la máquina virtual **VM0 no tenía habilitada la supervisión**.

![1.7](screenshots/1.7.png)

Seleccioné **Habilitar**, revisé la configuración por defecto y procedí a crear y habilitar la supervisión.

![1.8](screenshots/1.8.png)

Finalmente, confirmé que la **supervisión estaba correctamente habilitada** para la máquina virtual.

![1.9](screenshots/1.9.png)

---

## Tarea 2 – Crear una regla de alertas

Dentro de **Azure Monitor**, accedí al apartado de **Alertas** y creé una nueva **regla de alerta**.

![2.1](screenshots/2.1.png)

Seleccioné como **ámbito la suscripción**, de modo que la alerta se aplique a todas las máquinas virtuales.

![2.2](screenshots/2.2.png)

En la sección de **Condición**, busqué y seleccioné la señal **Delete Virtual Machine (Virtual Machines)**.

![2.3](screenshots/2.3.png)

En el área de **Lógica de alerta**, dejé los valores por defecto, manteniendo **Todos seleccionados**.

![2.4](screenshots/2.4.png)

A continuación, configuré las **acciones**, creando un grupo de acciones que se ejecutará cuando se cumpla la condición.

![2.5](screenshots/2.5.png)

---

## Tarea 3 – Configurar grupo de acciones y notificaciones

Creé un **grupo de acciones** llamado **Alert the operations team**.

![3.1](screenshots/3.1.png)

En la configuración de notificaciones, seleccioné el envío de un **correo electrónico**, asignándole el nombre **VM was deleted** y configurando la dirección de correo correspondiente.

![3.2](screenshots/3.2.png)

Revisé la configuración y creé el grupo de acciones.

![3.3](screenshots/3.3.png)

Después de crear el grupo de acciones, asigné un **nombre y descripción a la regla de alertas** y completé su creación.

![3.4](screenshots/3.4.png)

Verifiqué que la **regla de alertas se había creado correctamente** y aparecía activa en Azure Monitor.

![3.5](screenshots/3.5.png)

---

## Tarea 4 – Probar la alerta

Para validar la configuración, eliminé la máquina virtual **VM0**, lo que activó la regla de alertas configurada.

![4.1](screenshots/4.1.png)

> ⚠️ El correo de notificación puede tardar unos minutos en llegar.

---

## Tarea 5 – Configurar una regla de procesamiento de alertas

Para simular un escenario de **mantenimiento planificado**, creé una **regla de procesamiento de alertas** para suprimir notificaciones.

Desde **Alertas → Crear → Regla de procesamiento de alertas**, seleccioné el **ámbito de la suscripción**.

![5.1](screenshots/5.1.png)

Configuré la regla para **suprimir las notificaciones**.

![5.2](screenshots/5.2.png)

Definí un período específico de aplicación de la regla, estableciendo el horario entre **las 22:00 y las 07:00 del día siguiente**.

![5.3](screenshots/5.3.png)

Introduje el **nombre de la regla**, la **descripción** y el **grupo de recursos**.

![5.4](screenshots/5.4.png)

Tras revisar la configuración, creé la regla de procesamiento de alertas.

![5.5](screenshots/5.5.png)

---

## Tarea 6 – Consultar datos con Azure Monitor Logs

Por último, utilicé **Azure Monitor Logs** para consultar los datos capturados.

Accedí a **Azure Monitor → Registros** y seleccioné como **ámbito la suscripción**.

![6.1](screenshots/6.1.png)

En la parte superior derecha, cambié el modo de consulta a **Modo KQL** y ejecuté la consulta **Count heartbeats**.

![6.2](screenshots/6.2.png)

Posteriormente, ejecuté una consulta personalizada en KQL para analizar métricas de la máquina virtual.

Tras ejecutar la consulta, pude observar el **gráfico de heartbeats**, donde se identifica el momento en el que la máquina virtual dejó de enviar latidos, indicando su eliminación (aproximadamente a las **15:45**).

![6.3](screenshots/6.3.png)

---

## Limpieza

Para evitar costes innecesarios, eliminé todos los recursos creados durante el laboratorio.

La forma más sencilla es eliminar el grupo de recursos completo.

### Azure Portal
Grupo de recursos → Eliminar grupo de recursos

### Azure PowerShell
```powershell
Remove-AzResourceGroup -Name az104-rg11
