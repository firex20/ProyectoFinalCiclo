# Instalación del gestor Rancher en una maquia virtual y conexión con el cluster de Harvester

Rancher es un gestor de clusters de kubernetes que permite manejar varios clusters a la vez de distintos tipos, tanto clusteres on-premise como en la nube.

---

## Preparacion de la maquina virtual para Rancher

---

### Requisitos

Para instalar Rancher debemos tener un entorno con Kubernetes instalado, ya sea en un solo nodo o en varios. También tenemos otras opciones de instalacion como por ejemplo una instalacion autamatica desde EKS de amazon o una instalacion dentro de un contenedor de kubernetes. En este proyecto voy a usar una unica maquina virtual fuera del cluster de Harvester de este proyecto pero dentro de la red de la empresa, ya que los servidores no van a tener acceso directo desde fuera con ip publica, si no que sera la propia maquina de Rancher a la que se podra acceder desde fuera. Tampoco voy a usar el contenedor de Docker ya que esta recomendado solo para entornos de prueba.

Los requisitos de la maquina virtual que necesitaremos varian segun la cantidad de cluster y nodos que vayamos a manejar con esta instancia de Rancher.

| Deployment Size | Clusters | Nodes | vCPUs | RAM |
|------------------|----------|-------|-------|-----|
| Small | Up to 150 | Up to 1500 | 2 | 8 GB |
| Medium | Up to 300 | Up to 3000 | 4 | 16 GB |
| Large | Up to 500 | Up to 5000 | 8 | 32 GB |
| X-Large | Up to 1000 | Up to 10,000 | 16 | 64 GB |
| XX-Large | Up to 2000 | Up to 20,000 | 32 | 128 GB |

En el caso de este proyecto, ya que no voy a manejar una gran cantidad de nodos ni de clusters, usare una maquina con los requisitos minimos, es decir, 2 CPUs y 8GB de RAM. El espacio usado en disco es minimo, con lo cual serviria con 15-20GB.