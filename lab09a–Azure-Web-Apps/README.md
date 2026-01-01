# Lab 09a – Implementación de Web Apps (AZ-104)
Notas y documentación para el Lab 09a de AZ-104.

## Overview
En este laboratorio trabajé con **Azure Web Apps** para crear, configurar y gestionar aplicaciones web, incluyendo **deployment slots**, despliegue desde **GitHub** y configuración de **escalado automático**.  
El laboratorio abarca la creación de un App Service, configuración de slots de staging y producción, intercambio de slots, despliegue continuo desde un repositorio externo y pruebas de carga con **Azure Load Testing**.

El objetivo fue explorar cómo las Web Apps en Azure pueden ser desplegadas, probadas y escaladas automáticamente en un escenario real, asegurando disponibilidad y eficiencia.

## Business Scenario
La organización desea migrar sus sitios web desde un **centro de datos local (on-premises)** a Azure para reducir costes de hardware y mejorar la escalabilidad.  
Los sitios están actualmente en servidores Windows con PHP y el hardware está próximo a su fin de vida útil.  

Se creó un entorno que permite:

- Probar actualizaciones en un **slot de staging** antes de moverlas a producción.  
- Desplegar código automáticamente desde un **repositorio GitHub externo**.  
- Escalar automáticamente el App Service según la demanda mediante **autoscaling** y pruebas de carga.

## What I Learned
- Cómo crear y configurar un **App Service** en Azure para aplicaciones web PHP.  
- Cómo crear y gestionar **deployment slots** para staging y producción.  
- Cómo configurar **despliegue automático** desde un repositorio externo (GitHub).  
- Cómo realizar un **swap** entre slots de staging y producción.  
- Cómo configurar y probar **escalado automático** del App Service según la demanda.  
- Cómo utilizar **Azure Load Testing** para validar el rendimiento de la aplicación bajo carga.  
- Cómo monitorear métricas de cliente y servidor para asegurar el correcto funcionamiento del escalado.

## Full Documentation
Para instrucciones paso a paso con capturas en español, ver: **Lab09a Documentación Completa**
