# Despliegue de catálogo de aplicaciones sobre Kubernetes e Hiperconvergencia basada en Harverster y Rancher

## Descripción
El proyecto consistiría en el despliegue de una infraestructura de virtualización basada en kubernetes y kvm a través de un software llamado Harvester. El software de Harvester es un hipervisor opensource de ultima generación que permite crear tanto clústeres de kubernetes como maquinas virtuales con kvm de manera sencilla y eficiente, este software esta pensado para ser utilizado en el entorno empresarial y dentro de los hipervisores opensource es de los más modernos actualmente. La infraestructura consistirá en tres servidores físicos en los que se instalara Harvester y se creara un clúster de los tres, este cluster se gestionara a través del gestor Rancher, el cual se alojara en una maquina virtual separada de la infraestructura del clúster y a parte de permitirnos gestionar el clúster de Harvester nos permitirá ver distintas estadísticas sobre los host y las maquinas virtuales.

Dentro del  clúster de kubernetes se podrán desplegar y actualizar aplicaciones a través de una aplicación web llamada ArgoCD, esta aplicación permite conectar repositorios de Helm o Github y desplegarlos automáticamente en el clúster de kubernetes, también nos permite automatizar las actualizaciones de la aplicación que hemos instalado cada vez que se actualice el repositorio. Ademas, permite la creación de distintos usuarios con distintos permisos de acceso a los recursos y aplicaciones desplegadas y ofrece información sobre el estado de las aplicaciones y sus repositorios.

Todas las interfaces que estén expuestas a el exterior estarán securizadas con sus respectivos certificados y estarán asociadas a un nombre dns para su acceso.

También, al estar basada en clústeres y replicada y tener varios enlaces duplicados a la red, esta infraestructura tendrá alta disponibilidad.

---

## Hardware usado en el proyecto

---

## To-Do ✅ ❌ 🔜

- Preparar la infraestructura fisica
    - Funcion ✅
    - Documentación ✅
- Instalar Harvester y crear el cluster
    - Funcion ✅
    - Documentación ✅
- Instalar Rancher y conectarlo con Harvester
    - Funcion ✅
    - Documentación ✅
    - Automatizacion con ansible ❌
- Crear cluster de kubernetes desde rancher y testearlo
    - Funcion ❌
    - Documentación ❌
- Instalar ArgoCD y configurarlo
    - Funcion ❌
    - Documentación ❌
- Desplegar alguna aplicación desde gitHub o Helm
    - Funcion ❌
    - Documentación ❌
- Documento final recopilando toda la información del proyecto
- Preparar la presentacion y una prueba de despliegue
- (Extra) Instalar Prometheus y Grafana para control de recursos
- (Extra) Crear interfaz web propia para gestionar/desplegar aplicaciones

---

## Errores y problemas encontrados durante el desarrollo del proyecto
- Al intentar instalar harvester conectandolo al switch con el puerto en modo trunk para aceptar vlan tageadas no era posible conectarse, hay que poner el puerto del switch en modo acceso.
- Al instalar docker para rancher hay que instalar una versión concreta, no sirven las ultimas.

---

## Documentación y manuales usados para el proyecto

`Instalacion Harvester`

- https://docs.harvesterhci.io/v1.1
- https://www.balena.io/etcher

`Instalacion Rancher`

- https://rockylinux.org/
- https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-rocky-linux-9
- https://docs.docker.com/engine/install/centos/
- https://www.tecmint.com/open-port-for-specific-ip-address-in-firewalld/
- https://ranchermanager.docs.rancher.com/v2.5/getting-started/installation-and-upgrade/installation-requirements/port-requirements
- https://rke.docs.rancher.com/installation
- https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/
- https://www.rancher.com/
- https://ranchermanager.docs.rancher.com

---

## Pato paco

<img src="Misc/paco.jpeg" width="200" alt="El Paco" title="El Paco te roba el tabaco ;)">