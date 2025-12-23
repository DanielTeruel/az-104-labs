# Lab 02b – Gestionar la gobernanza mediante Azure Policy (AZ-104)

## Resumen
En este laboratorio me centré en implementar gobernanza en Azure utilizando **Azure Policy**, **etiquetado de recursos** y **bloqueos de recursos**.  
El objetivo fue garantizar el cumplimiento de estándares organizativos, mejorar la trazabilidad de costes y proteger recursos críticos frente a cambios o eliminaciones accidentales.

---

## Escenario de negocio (Roleplay)

En mi escenario, la organización ha incrementado significativamente su presencia en Azure durante el último año.  
Tras una auditoría interna, se detectaron múltiples recursos sin información clave como propietario, proyecto o centro de costes.

Para mejorar la gobernanza y el control operativo, se decidió:
- Estandarizar el uso de etiquetas en los recursos
- Forzar el etiquetado mediante Azure Policy
- Corregir automáticamente recursos no conformes
- Proteger recursos críticos usando bloqueos

Estas acciones están alineadas con el **Microsoft Well-Architected Framework** y el **Cloud Adoption Framework**.

---

## Objetivos del laboratorio

- Crear y aplicar etiquetas a grupos de recursos
- Forzar el uso de etiquetas mediante Azure Policy
- Heredar etiquetas automáticamente desde el grupo de recursos
- Corregir errores de cumplimiento mediante remediación
- Configurar y validar bloqueos de recursos
- Limpiar los recursos al finalizar

---

## Tarea 1 – Crear el grupo de recursos y asignar etiquetas

Lo primero que hice fue crear el grupo de recursos donde se ejecutaría todo el laboratorio.

- **Nombre del grupo de recursos:** `az104-rg2`
- **Región:** Spain Central

![Create resource group](screenshots/1.1.png)

Una vez creado, navegué al apartado **Etiquetas** del grupo de recursos y añadí la siguiente etiqueta:

- **Nombre:** Centro de costes  
- **Valor:** 000  

![Add Cost Center tag](screenshots/1.2.png)

---

## Tarea 2 – Forzar el etiquetado mediante Azure Policy

Desde el portal de Azure, navegué a **Directivas (Azure Policy)** y, dentro de **Definiciones**, busqué la directiva integrada:

**Require a tag and its value on resources**

![Policy definition search](screenshots/2.1.png)

Asigné la directiva estableciendo como **ámbito** el grupo de recursos `az104-rg2`.

![Select policy scope](screenshots/2.2.png)

Configuré los datos de identificación de la asignación de la directiva, dejando el cumplimiento habilitado.

![Policy assignment details](screenshots/2.3.png)

Antes de crearla, revisé que la configuración fuera correcta.

![Review policy assignment](screenshots/2.4.png)

Una vez creada, accedí al apartado de **Cumplimiento** para verificar que la directiva estaba activa y correctamente aplicada.

![Policy compliance status](screenshots/2.5.png)

Para comprobar el funcionamiento, intenté crear una **cuenta de almacenamiento** dentro del grupo de recursos.

![Create storage account attempt](screenshots/2.6.png)

El despliegue falló debido a que la cuenta no incluía la etiqueta requerida, lo que confirma que la directiva estaba funcionando correctamente.

![Policy validation failed](screenshots/2.7.png)  
![Policy error details](screenshots/2.8.png)

A continuación, probé a **eliminar la asignación de la directiva**.

![Delete policy assignment](screenshots/2.9.png)  
![Policy removed](screenshots/2.10.png)

Tras eliminarla, al volver a crear una cuenta de almacenamiento, el despliegue se realizó correctamente, ya que la restricción ya no estaba aplicada.

---

## Tarea 3 – Heredar etiquetas automáticamente desde el grupo de recursos

Volví a **Directivas → Asignaciones → Asignar directiva** y seleccioné la definición:

**Inherit a tag from the resource group if missing**

Configuré el ámbito en el grupo de recursos `az104-rg2`, añadí nombre y descripción, y habilité el cumplimiento.

![Assign inherit tag policy](screenshots/3.1.png)

En el apartado de **Corrección (Remediation)**, habilité **Crear una tarea de corrección** y seleccioné la política correspondiente.  
También configuré correctamente el parámetro del nombre de la etiqueta.

![Enable remediation task](screenshots/3.2.png)

Revisé la configuración antes de crear la asignación.

![Review remediation settings](screenshots/3.3.png)

A continuación, creé una nueva cuenta de almacenamiento dentro del grupo de recursos.

![Create storage account](screenshots/3.4.png)

En este punto detecté un problema:  
En el grupo de recursos, la etiqueta estaba definida como **“Centro de costes”**, mientras que en la directiva utilicé **“Cost Center”**.  
Debido a esta diferencia, la política no se aplicó correctamente.

![Tag mismatch issue](screenshots/3.5fallo.png)

Para solucionarlo, añadí la etiqueta **Cost Center** al grupo de recursos para que coincidiera exactamente con la definida en la asignación de la política.

![Fix tag name](screenshots/3.6fix.png)

Tras repetir el proceso con la corrección aplicada, la política heredó correctamente la etiqueta y la aplicó automáticamente al recurso.

![Policy applied successfully](screenshots/3.7.png)

---

## Tarea 4 – Configurar y probar bloqueos de recursos

Finalmente, navegué al grupo de recursos y accedí a **Configuración → Bloqueos**.  
Creé un bloqueo con los siguientes valores:

- **Nombre:** rg-lock  
- **Tipo:** Eliminar  

![Create delete lock](screenshots/4.1.png)

Intenté eliminar la cuenta de almacenamiento dentro del grupo de recursos.

![Attempt to delete resource](screenshots/4.2.png)

La eliminación fue bloqueada correctamente, confirmando que el bloqueo se aplicó de forma satisfactoria y protegía el recurso.

![Deletion blocked](screenshots/4.4.png)

---

## Limpieza

Para evitar costes innecesarios y mantener el entorno limpio, eliminé todos los recursos creados durante el laboratorio.  
Primero eliminé el bloqueo y, posteriormente, el grupo de recursos, asegurándome de que no quedaran recursos activos ni cargos asociados.

---

