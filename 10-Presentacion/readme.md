# Guia de la presentacion/demostración del proyecto

1. Descripción del proyecto:
    
    - Este proyecto consiste en el montaje de la infraestructura y software necesario para poner en funcionamiento un cluster hibrido de maquinas virtuales/kubernetes que permitira tanto desplegar maquinas virtuales enteras como desplegar aplicaciones, mantenerlas actualizadas automaticamente y ofrecerlas a traves de internet.

2. Infraestructura y hardware

    - 3 servidores, 128 gb ram, CPU 8 cores 3.xghz, 2 discos de 800GB y 2 de 980GB cada servidro y 9 interfazes ethernet 1gb/s.
    - Switch inteligente.
    - Maquina virtual (rancher), 4cores, 8gb ram, 40 gb disco.

3. Network

    - Esquema de la configuración de la red

4. Rancher-harvester

    - Presentación rapida de la interfaz rancher: Clusters, virtualization manager, user management, global settings y extensions

    - Presentacion rapida harvester, crear maquina virtual.

5. Cluster de kubernetes

    - Presentación rapida cluster kubernetes

6. ArgoCD

    - Despliegue de aplicación de ejemplo
    - Hablar del certificado de letsencrypt

7 Acceso a aplicaciones

    - Haproxy-ingress

