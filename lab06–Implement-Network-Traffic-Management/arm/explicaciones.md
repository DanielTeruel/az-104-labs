Explicación de la plantilla ARM

Esta plantilla ARM despliega un entorno simple de 2 máquinas virtuales (VMs) en Azure con la configuración mínima necesaria para un laboratorio, sin instalar IIS, y con el objetivo de reducir costos usando solo 2 VMs.

1. Parámetros

Los parámetros permiten que la plantilla sea flexible. Se definen nombres, contraseñas y recursos de red.

virtualMachines_az104_06_vm0_name / vm1_name: Nombres de las dos VMs.

virtualNetworks_az104_06_vnet1_name: Nombre de la red virtual (VNet).

networkInterfaces_az104_06_nic0_name / nic1_name: Nombres de las interfaces de red asociadas a cada VM.

networkSecurityGroups_az104_06_nsg1_name: Nombre del grupo de seguridad de red (NSG).

adminPassword: Contraseña segura para los administradores de las VMs.

Los parámetros permiten reutilizar la plantilla sin modificarla directamente.
