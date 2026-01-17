# Lab 11 – Implement Monitoring (AZ-104)

## Tarea 1 – Desplegar infraestructura y habilitar Azure Monitor

Para comenzar el laboratorio, accedí al **Portal de Azure** y seleccioné **Implementar una plantilla personalizada**.

### Implementación de la plantilla ARM

Creé una plantilla personalizada utilizando el **editor de plantillas** del portal.

![1.1](screenshots/1.1.png)

A continuación, implementé la plantilla ARM correspondiente al laboratorio.
*(El código completo de la plantilla se documentará más adelante).*

* 📄 [az104-11-vm-template.json](arm/az104-11-vm-template.json)

![1.2](screenshots/1.2.png)

Durante el despliegue, introduje el **usuario y contraseña** en el apartado **Detalles de la instancia**, necesarios para la creación de la máquina virtual.

![1.3](screenshots/1.3.png)

Una vez finalizada la implementación, verifiqué que **todos los recursos se habían desplegado correctamente** dentro del grupo de recursos.

![1.4](screenshots/1.4.png)

### Configuración de Azure Monitor y VM Insights

Después del despliegue, accedí al servicio **Azure Monitor**.

![1.5](screenshots/1.5.png)

Desde Azure Monitor, fui a la información de la máquina virtual y accedí a la configuración de **Insights**.

![1.6](screenshots/1.6.png)

En el apartado de información general, comprobé que la máquina virtual **VM0 no tenía habilitada la supervisión**.

![1.7](screenshots/1.7.png)

Seleccioné **Habilitar**, revisé la configuración por defecto y procedí a **crear y habilitar la supervisión**.

![1.8](screenshots/1.8.png)

Tras completar el proceso, confirmé que la **supervisión estaba correctamente habilitada** para la máquina virtual.

![1.9](screenshots/1.9.png)

---

## Tarea 2 – Crear una regla de alertas

Dentro de **Azure Monitor**, accedí al apartado de **Alertas** y creé una nueva **regla de alerta**.

![2.1](screenshots/2.1.png)

Seleccioné como **ámbito la suscripción** y apliqué la selección para que la alerta se activara ante eventos en cualquier máquina virtual.

![2.2](screenshots/2.2.png)

En la sección de **Condición**, busqué y seleccioné la señal **Delete Virtual Machine (Virtual Machines)**, que se activará cuando una máquina virtual sea eliminada.

![2.3](screenshots/2.3.png)

En el área de **Lógica de alerta**, dejé los valores por defecto, manteniendo **Todos seleccionados**.

![2.4](screenshots/2.4.png)

A continuación, configuré las **acciones** creando un grupo de acciones que se ejecutará cuando la condición se cumpla.

![2.5](screenshots/2.5.png)

---

## Tarea 3 – Configurar el grupo de acciones

Creé un **grupo de acciones** llamado **Alert the operations team**.

![3.1](screenshots/3.1.png)

En la configuración de notificaciones, seleccioné el envío de un **correo electrónico**, asignándole el nombre **VM was deleted** y configurando la dirección de correo correspondiente.

![3.2](screenshots/3.2.png)

Una vez revisada la configuración, creé el grupo de acciones.

![3.3](screenshots/3.3.png)

Después de crear el grupo de acciones, asigné un **nombre a la regla de alertas** y completé la creación de la misma.

![3.4](screenshots/3.4.png)

Finalmente, confirmé que la **regla de alertas se había creado correctamente** y aparecía activa en Azure Monitor.

![3.5](screenshots/3.5.png)

---

## Tarea 4 – Probar la alerta

Para validar el funcionamiento de la alerta, realicé una prueba eliminando la máquina virtual **VM0**, lo que provocó la activación de la regla de alertas.

![4.1](screenshots/4.1.png)

> ⚠️ El correo y la notificación pueden tardar unos minutos en llegar. Mientras tanto, continué con la siguiente tarea.

---

## Tarea 5 – Crear una regla de procesamiento de alertas

Con el objetivo de simular un escenario de **mantenimiento programado**, creé una **regla de procesamiento de alertas** para suprimir notificaciones durante un período específico.

Desde **Alertas → Crear → Regla de procesamiento de alertas**, seleccioné nuevamente el **ámbito de la suscripción**.

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

Por último, utilicé **Azure Monitor Logs** para consultar los datos capturados de la máquina virtual.

Accedí a **Azure Monitor → Registros** y seleccioné como **ámbito la suscripción**.

![6.1](screenshots/6.1.png)

En la parte superior derecha, cambié el modo de consulta a **Modo KQL** y busqué la consulta **Count heartbeats**.

![6.2](screenshots/6.2.png)

A continuación, ejecuté una consulta personalizada en KQL.
*(El código de la consulta se documentará más adelante mediante un hiperenlace).*

Tras ejecutar la consulta, pude observar el **gráfico de heartbeats**, donde se identifica claramente el momento en el que la máquina virtual dejó de enviar latidos, indicando que fue eliminada. En este caso, la VM fue borrada aproximadamente a las **15:45**.

![6.3](screenshots/6.3.png)
