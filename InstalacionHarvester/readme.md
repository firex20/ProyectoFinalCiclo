# Instalación y creacion de cluster de Harvester

## Requisitos
Para poder instalar Harvester en un servidor o en una maquina virtual primero debe cumplir unos requisitos minimos:
- Solo adminte CPUs x86_64 compatibles con virtualización y con un minimo de 8 cores.
- Minimo 32GB de memoria RAM, aunque en la documentación oficial recomiendan 64GB para un entorno en producción. Con menos de 32GB puede funcionar, pero de manera limitada.
- Minimo 200GB de capacidad en disco, 500GB recomendados para un entorno en producción. Durante la instalación tambien recomienda separar en discos distintos la instalación de Harvester de el almacenamiento reservado para las maquinas virtuales.


## Instalación del primer nodo
