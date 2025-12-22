# Lab 02a – Gestionar suscripciones y RBAC (AZ-104)

## Resumen
En este laboratorio me centré en gestionar suscripciones de Azure e implementar el control de acceso basado en roles (RBAC) usando Grupos de Administración.  
El objetivo fue aplicar el principio de menor privilegio mientras centralizaba la gestión de accesos a gran escala.

---

## Escenario de negocio (Roleplay)

En mi escenario, la organización está creciendo y gestionando múltiples suscripciones de Azure.  
Para simplificar la gobernanza y el control de accesos, organicé las suscripciones usando Grupos de Administración.

Un equipo centralizado de Help Desk es responsable de:
- Gestionar máquinas virtuales
- Crear y gestionar solicitudes de soporte en Azure

Sin embargo, este equipo **no debe**:
- Registrar nuevos proveedores de recursos de Azure
- Modificar recursos más allá de sus responsabilidades

Para ello, implementé una combinación de roles RBAC integrados y personalizados a nivel del Grupo de Administración.

---

## Objetivos del laboratorio

- Crear y configurar Grupos de Administración
- Asignar roles RBAC integrados
- Crear un rol RBAC personalizado
- Revisar asignaciones de roles usando el Registro de Actividad
- Limpiar los recursos al finalizar

---

## Tarea 1 – Crear y configurar un Grupo de Administración

Navegué a **Grupos de Administración** en el portal de Azure (usando la barra de búsqueda central).

![Create Management Group](screenshots/1.1.png)  

Creé un nuevo Grupo de Administración con la siguiente configuración:
- **ID del Grupo de Administración:** `az104-mg1`
- **Nombre de visualización:** `az104-mg1`

![Management Group creation in progress](screenshots/1.2.png)

Después de unos momentos, verifiqué que el Grupo de Administración apareciera en la jerarquía.

![Management Group visible in hierarchy](screenshots/1.3.png)

---

## Tarea 2 – Asignar un rol RBAC integrado

Dentro del Grupo de Administración `az104-mg1`, navegué a **Control de acceso (IAM)** y seleccioné **Agregar → Asignar rol**.

![Add role assignment](screenshots/2.1.png)  

Asigné el rol de **Colaborador de Máquina Virtual** al grupo Help Desk.

![Select VM Contributor role](screenshots/2.2.png)  

Este rol permitió al equipo gestionar máquinas virtuales sin otorgar acceso al sistema operativo ni a la red y almacenamiento asociados.

![Select Help Desk group](screenshots/2.3.png)  

Verifiqué la asignación en **Control de acceso (IAM) → Asignaciones de roles**.

![Role assignment completed](screenshots/2.4.png)

---

## Tarea 3 – Crear un rol RBAC personalizado

Desde **Control de acceso (IAM)**, seleccioné **Agregar → Rol personalizado**.

![Custom role creation start](screenshots/3.1.png)  

Configuré el rol con lo siguiente:
- **Nombre del rol:** `Custom Support Request`
- **Descripción:** Un rol personalizado para solicitudes de soporte
- **Permisos base:** Clonar rol
- **Rol a clonar:** Support Request Contributor

![Select role to clone](screenshots/3.2.png)  

Excluí el permiso:
- **Other: Register Support Resource Provider**

![Enter role details](screenshots/3.3.png)  

Este permiso se añadió como **NotAction**, evitando que el equipo de Help Desk registre nuevos proveedores de recursos de Azure.

![Exclude permissions step 1](screenshots/3.4.png)  

Verifiqué que el ámbito asignable estuviera configurado al Grupo de Administración `az104-mg1` y revisé el JSON generado antes de crear el rol.

![Exclude permissions step 2](screenshots/3.5.png)  
![Review role JSON](screenshots/3.6.png)  

Finalmente, confirmé que el rol personalizado se creó correctamente.

![Custom role created](screenshots/3.7.png)

---

## Tarea 4 – Revisar cambios de roles usando el Registro de Actividad

Navegué al **Registro de Actividad** del Grupo de Administración `az104-mg1` para revisar:
- Asignaciones de roles
- Creación del rol RBAC personalizado
- Otros cambios administrativos realizados durante el laboratorio

![Activity Log overview](screenshots/4.1.png)  
![Filter activity log](screenshots/4.2.png)  

---

## Limpieza

Para evitar costos innecesarios y configuraciones sobrantes, eliminé el Grupo de Administración al finalizar el laboratorio.

### Azure Portal
Grupo de Administración → General → Eliminar

![Filter activity log](screenshots/4.2.png)  

### Azure PowerShell
Alternativamente, el Grupo de Administración también se puede eliminar usando Azure PowerShell.
```powershell
Remove-AzManagementGroup -GroupName az104-mg1
```
Una vez finalizada la limpieza, navegué al panel Grupos de administración para verificar que el grupo se había eliminado correctamente.

![Custom role creation activity](screenshots/4.4.png)
