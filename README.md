# Despliegue de catálogo de aplicaciones sobre Kubernetes e Hiperconvergencia basada en Harverster y Rancher

## Descripción
El proyecto consistiría en el despliegue de una infraestructura de virtualización basada en kubernetes y kvm a través de un software llamado Harvester. El software de Harvester es un hipervisor opensource de ultima generación que permite crear tanto clústeres de kubernetes como maquinas virtuales con kvm de manera sencilla y eficiente, este software esta pensado para ser utilizado en el entorno empresarial y dentro de los hipervisores opensource es de los más modernos actualmente. La infraestructura consistirá en tres servidores físicos en los que se instalara Harvester y se creara un clúster de los tres, este cluster se gestionara a través del gestor Rancher, el cual se alojara en una maquina virtual separada de la infraestructura del clúster y a parte de permitirnos gestionar el clúster de Harvester nos permitirá ver distintas estadísticas sobre los host y las maquinas virtuales.

Dentro del  clúster de kubernetes se podrán desplegar y actualizar aplicaciones a través de una aplicación web llamada ArgoCD, esta aplicación permite conectar repositorios de Helm o Github y desplegarlos automáticamente en el clúster de kubernetes, también nos permite automatizar las actualizaciones de la aplicación que hemos instalado cada vez que se actualice el repositorio. Ademas, permite la creación de distintos usuarios con distintos permisos de acceso a los recursos y aplicaciones desplegadas y ofrece información sobre el estado de las aplicaciones y sus repositorios.

Todas las interfaces que estén expuestas a el exterior estarán securizadas con sus respectivos certificados y estarán asociadas a un nombre dns para su acceso.

También, al estar basada en clústeres y replicada y tener varios enlaces duplicados a la red, esta infraestructura tendrá alta disponibilidad.

---

## Hardware usado en el proyecto

---

## To-Do ✅ ❌

- Preparar la infraestructura fisica
    - Funcion ❌
    - Documentación ❌
- Instalar Harvester y crear el cluster
    - Funcion ❌
    - Documentación ❌
- Instalar Rancher y conectarlo con Harvester
    - Funcion ❌
    - Documentación ❌
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

---

## Documentación y manuales usados para el proyecto