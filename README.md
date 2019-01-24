# Infraestructura de gestión ferroviaria basada en la nube

[![Build Status](https://travis-ci.com/migueldgoncalves/CCproj_1819.svg?branch=master)](https://travis-ci.com/migueldgoncalves/CCproj_1819)

## Resúmen

Esta aplicación se trata de mi proyecto de la asignatura de Cloud Computing: Fundamentos e Infraestructuras.

Este proyecto se destina a desplegar en la nube infraestructura virtual de apoyo a una compañía ferroviaria fictícia que gestionará una red de ferrocarriles suburbanos centrados en Granada, España. Servirá tanto a clientes de la compañía como a todos sus empleados.

Los clientes podrán acceder a la aplicación para consultar horarios de trenes, precios de viajes y noticias de ámbito ferroviario. Además, podrán comprar viajes y gestionar sus reservas, e. g. cambiar su asiento o cancelarlas.

Los empleados podrán adicionalmente gestionar viajes, e. g. añadir nuevas o cambiar su horario, y información disponible al público; así como gestionar y monitorizar equipo ferroviario como señalización y cámaras de seguridad.

La aplicación cuentará con un servicio de autenticación disponible a todos los usuarios que atribuirá a cada uno los permisos adecuados, asegurando que clientes y empleados con diferentes funciones acceden solamente a las funcionalidades debidas de la aplicación.

Este proyecto seguirá siendo desarrollado hasta Enero, cuando se prevé su conclusión.

No está relacionado con ningún otro, sea de TFG, TFM o de una empresa. Se destina únicamente a la asignatura de Cloud Computing e seré yo su cliente.

Está distribuido con la licencia MIT.

Despliegue https://javalinapp.herokuapp.com/

MV: 

MV2: 

### Estado actual



## Enlaces de interés relacionados con el hito actual

* [Funcionamiento de la aplicación](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/funcionamiento.md)

  * [Información al Cliente](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/funcionamiento.md#informaci%C3%B3n-al-cliente)
  
  * [Gestión de Viajes](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/funcionamiento.md#gesti%C3%B3n-de-viajes)
  
  * [Integración](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/funcionamiento.md#integraci%C3%B3n)
  
  * [Variables de entorno](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/funcionamiento.md#variables-de-entorno)
  
  * [Motivo de la infraestructura desplegada](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/funcionamiento.md#motivo-de-la-infraestructura-desplegada)
  
* [Justificación de elecciones - Provisionamiento](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/justificacion_hito4.md)

  * [Sistema cloud](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/justificacion_hito4.md#sistema-cloud)
  
  * [Región](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/justificacion_hito4.md#regi%C3%B3n)
  
  * [Imagen](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/justificacion_hito4.md#imagen)
  
  * [Tamaño de la máquina virtual](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/justificacion_hito4.md#tama%C3%B1o-de-la-m%C3%A1quina-virtual)
  
  * [Tamaño del disco de datos](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/justificacion_hito4.md#tama%C3%B1o-del-disco-de-datos)

## Enlaces de interés relacionados con los hitos anteriores

* [Arquitectura y características de la aplicación](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/arquitectura.md) - Actualizado
    
  * [Información al Cliente](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/arquitectura.md#informaci%C3%B3n-al-cliente) - Actualizado
  
  * [Autenticación](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/arquitectura.md#autenticaci%C3%B3n)
  
  * [Gestión de Viajes](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/arquitectura.md#gesti%C3%B3n-de-viajes) - Actualizado
  
  * [Gestión de Equipos](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/arquitectura.md#gesti%C3%B3n-de-equipos)

* [Comprobación de playbooks de Ansible - 1 máquina virtual](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/comprobacion.md)

  * [Comprobación de otros playbooks](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/comprobacion.md#comprobaci%C3%B3n-de-otros-playbooks)
  
  * [Comprobación de mi playbook](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/comprobacion.md#comprobaci%C3%B3n-de-mi-playbook)

* [Configuración del despliegue - Heroku](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/despliegue_PaaS.md)

* [Configuración del PaaS - Heroku](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/PaaS_configuracion.md)

* [Creación de la aplicación - Microservicio básico](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/creacion_aplicacion.md)

* [Elección del PaaS](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/PaaS_eleccion.md)

* [Hitos del proyecto](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/hitos.md)

* [Motivo de la aplicación](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/motivo.md)

* [Playbook de Ansible](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/playbook.md) - Actualizado

  * [Playbook](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/playbook.md#playbook) - Actualizado
  
    * [Instalación del Java 8, Git y Maven](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/playbook.md#instalaci%C3%B3n-del-java-8-git-y-maven)
    
    * [Instalación y arranque del MongoDB](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/playbook.md#instalaci%C3%B3n-y-arranque-del-mongodb)
    
    * [Despliegue de la aplicación](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/playbook.md#despliegue-de-la-aplicaci%C3%B3n) - Actualizado

* [Provisionamiento con Ansible - Creación automática de las máquinas virtuales](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/provisionamiento_auto.md) - Actualizado

  * [Preparación](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/provisionamiento_auto.md#preparaci%C3%B3n) - Actualizado
  
  * [Variables del script](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/provisionamiento_auto.md#variables-del-script) - Actualizado
  
  * [Creación, configuración y provisionamiento de las MVs, y despliegue de la aplicación](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/provisionamiento_auto.md#creaci%C3%B3n-configuraci%C3%B3n-y-provisionamiento-de-la-mv-y-despliegue-de-la-aplicaci%C3%B3n) - Actualizado

* [Provisionamiento con Ansible - Creación manual de las máquinas virtuales](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/provisionamiento_manual.md) - Actualizado

  * [Instalación y configuración de Ansible](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/provisionamiento_manual.md#instalaci%C3%B3n-y-configuraci%C3%B3n-de-ansible) - Actualizado
  
  * [Creación y configuración de las máquinas virtuales](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/provisionamiento_manual.md#creaci%C3%B3n-y-configuraci%C3%B3n-de-la-m%C3%A1quina-virtual) - Actualizado
  
  * [Provisionamiento de las máquinas virtuales y despliegue remoto de la aplicación](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/provisionamiento_manual.md#provisionamiento-de-la-m%C3%A1quina-virtual-y-despliegue-remoto-de-la-aplicaci%C3%B3n) - Actualizado

[Rutas](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/rutas.md)

[Script de provisionamiento](https://github.com/migueldgoncalves/CCproj_1819/blob/master/docs/script_azure_cli.md)