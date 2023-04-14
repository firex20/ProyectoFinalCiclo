# Instalación y creacion de cluster de Harvester

## Requisitos
Para poder instalar Harvester en un servidor o en una maquina virtual primero debe cumplir unos requisitos minimos:
- Solo adminte CPUs x86_64 compatibles con virtualización y con un minimo de 8 cores.
- Minimo 32GB de memoria RAM, aunque en la documentación oficial recomiendan 64GB para un entorno en producción. Con menos de 32GB puede funcionar, pero de manera limitada.
- Minimo 200GB de capacidad en disco, 500GB recomendados para un entorno en producción. Durante la instalación tambien recomienda separar en discos distintos la instalación de Harvester de el almacenamiento reservado para las maquinas virtuales.
- Los discos de almacenamiento deben ser SSD con una velocidad minima de 5000 IOPS.
- Minimo 1 interfaz ethernet de 1Gbps

## Instalación
### Primer nodo
1. Para instalar Harvester descargarmos la ISO desde el [repositorio oficial](https://github.com/harvester/harvester/releases) de la versión que queremos instalar, se recomienda siempre usar la última versión de release, actualmente es la 1.1.1 (Esta es la forma de instalación manual, tambien se podria instalar de manera automatica a traves de la red con PXE, pero para este proyecto lo haremos de forma manual)

2. Una vez descargado, creamos una unidad USB booteable con la ISO usando cualquier programa que sirva para ello, como por ejemplo [Balena Etcher](https://www.balena.io/etcher) o [Rufus](https://rufus.ie/es/)

3. Conectamos la memoria USB a el servidor y encendemos la maquina, seleccionamos el instalador cuando aparezca el menu y seguimos el asistente. Dentro de este asistente tendremos que seleccionar las siguientes opciones:
    - La primera opción sera si queremos crear un nuevo cluster o añadir el nodo a uno existente, como es el primer nodo que instalamos creamos uno nuevo.
    - Lo siguiente sera elegir en que disco se instalara el sistema y en que disco se guardaran los datos de las maquinas virtuales, segun recomiendan es mejor que sean dos discos distintos, pero se puede usar el mismo. A la hora de elegirlo, el disco del sistema debe tener como minimo 200GB y el de VM no tiene minimo pero recomienda que sea de 140GB.