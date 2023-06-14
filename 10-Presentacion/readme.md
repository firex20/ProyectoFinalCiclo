# Guia de la presentacion/demostración del proyecto

1. Descripción del proyecto:
    
    - Este proyecto consiste en el montaje de la infraestructura y software necesario para poner en funcionamiento un cluster hibrido de maquinas virtuales/kubernetes que permitira tanto desplegar maquinas virtuales enteras como desplegar aplicaciones, mantenerlas actualizadas automaticamente y ofrecerlas a traves de internet.

2. Infraestructura y recursos usados

    - 3 servidores, 128 gb ram, CPU 8 cores 2.6ghz, 2 discos de 800GB y 2 de 980GB cada servidro y 9 interfazes ethernet 1gb/s.
    - Switch inteligente.
    - Maquina virtual (rancher), 4cores, 8gb ram, 40 gb disco.
    - Ip publica (Maquina rancher)
    - DNS (Propio y de trevenque)

3. Network

    - Esquema de la configuración de la red
    - 3 vlans (management, vm y storage)
    - Conexion a traves de la maquina de rancher

4. Rancher-harvester

    - Presentación rapida de la interfaz rancher: Clusters, virtualization manager, user management, global settings y extensions.
    - Playbook de ansible para rancher.
    - Presentacion rapida harvester, crear maquina virtual.
    - Monitorización (Prometheus y Grafana).

5. Cluster de kubernetes

    - Mostrar nodos.
    - Presentación rapida cluster kubernetes
    - Longhorn
    - Monitorización (Prometheus y Grafana).

6. ArgoCD

    - Aplicacion desplegada, nextcloud (Pedro, 20022002P_e)
    - Despliegue de aplicación de ejemplo ( https://github.com/firex20/helm-example-app )
    - Hablar del certificado de letsencrypt

7. Acceso a aplicaciones

    - Haproxy-Ingress
    - Certificado letsencrypt

8. Github

    - Hablar sobre el github con toda la documentación del proceso creada siguiendo el proceso de git flow.