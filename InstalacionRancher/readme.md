# Instalación del gestor Rancher en una maquia virtual y conexión con el cluster de Harvester

Rancher es un **gestor de clusters de kubernetes** que permite manejar varios clusters a la vez de distintos tipos, tanto clusteres on-premise como en la nube.

En este documento voy a explicar como he instalado Rancher en una maquina virtual de manera manual, pero tendre preparado un playbook de ansible para hacerlo todo de manera automatica a traves de ssh.

---

## Preparacion de la maquina virtual para Rancher

### **Requisitos**

Para instalar Rancher debemos tener un entorno con **Kubernetes** instalado, ya sea en un solo nodo o en varios. También tenemos otras opciones de instalacion como por ejemplo una instalacion autamatica desde EKS de amazon o una instalacion dentro de un contenedor de kubernetes. En este proyecto voy a usar una unica **maquina virtual** fuera del cluster de Harvester de este proyecto pero dentro de la red de la empresa, ya que los servidores no van a tener acceso directo desde fuera con ip publica, si no que sera la propia maquina de Rancher a la que se podra acceder desde fuera. Tampoco voy a usar el contenedor de Docker ya que esta recomendado solo para entornos de prueba.

Los requisitos de la maquina virtual que necesitaremos varian segun la cantidad de cluster y nodos que vayamos a manejar con esta instancia de Rancher, tambien depende de el tipo de instalación que se vaya a realizar, en mi caso, como voy a hacer un cluster de un solo nodo de kubernetes para alojar el servidor de Rancher los requisitos que se aplican son los siguientes:

| Deployment Size | Clusters | Nodes | vCPUs | RAM |
|------------------|----------|-------|-------|-----|
| Small | Up to 150 | Up to 1500 | 2 | 8 GB |
| Medium | Up to 300 | Up to 3000 | 4 | 16 GB |
| Large | Up to 500 | Up to 5000 | 8 | 32 GB |
| X-Large | Up to 1000 | Up to 10,000 | 16 | 64 GB |
| XX-Large | Up to 2000 | Up to 20,000 | 32 | 128 GB |

En el caso de este proyecto, ya que no voy a manejar una gran cantidad de nodos ni de clusters, usare una maquina con los requisitos minimos, es decir, 2 CPUs y 8GB de RAM. El espacio usado en disco es minimo ya que no vamos a usar realmete este cluster para alojar más maquinas virtuales, con lo cual serviria con 15-20GB.

### **Sistema operativo**

Rancher es compatible con cualquier sistema operativo linux que tenga instalado un cluster de kubernetes, que a su vez, se puede instalar practicamente en cualquier distribucion linux que sea compatible con Docker. En este proyecto voy a usar la última version de **Rocky Linux** como sistema operativo ya que es un SO opensource, ligero y compatible con todo lo necesario para instalar Rancher. Usare una instalación minima ya que para instalar todo lo necesario solo me hara falta el sistema base y acceso con ssh.

Una vez instalado el sistema operativo procedo a empezar a instalar todos los paquetes necesarios para Rancher.

### **Configuracion basica del firewall**

Tras acceder al servidor como root, lo primero que hay que hacer es securizar con un firewall el acceso a este servidor ya que va a estar expuesto a internet, para ello instalo e inicio el paquete de **firewalld**:

```console
dnf install firewalld -y
systemctl start firewalld
```
Normalmente antes de iniciar cualquier firewall habria que permitir primero el acceso ssh a el servidor para no quedarnos sin conexion con el servidor, pero la configuración por defecto de firewalld ya permite conexiones ssh.
Para comprobar que se ha instalado correctamente:
```console
systemctl status firewalld
```
```
● firewalld.service - firewalld - dynamic firewall daemon
     Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; vendor preset: enabled)
     Active: active (running) since Thu 2023-04-20 10:30:58 CEST; 13min ago
       Docs: man:firewalld(1)
   Main PID: 717 (firewalld)
      Tasks: 2 (limit: 23124)
     Memory: 43.5M
        CPU: 550ms
     CGroup: /system.slice/firewalld.service
             └─717 /usr/bin/python3 -s /usr/sbin/firewalld --nofork --nopid

Apr 20 10:30:57 localhost systemd[1]: Starting firewalld - dynamic firewall daemon...
Apr 20 10:30:58 localhost systemd[1]: Started firewalld - dynamic firewall daemon.
```

Por último, añado los servicios y puertos que me van a hacer falta para acceder a Rancher y limito el acceso ssh solo para la red interna.

```console
firewall-cmd --permanent --add-service=http
firewall-cmd --permanent --add-service=https
firewall-cmd --remove-service=ssh --permanent
firewall-cmd --new-zone=red-interna --permanent
firewall-cmd --zone=red-interna --add-source=192.168.56.0/24 --permanent
firewall-cmd --zone=red-interna --add-service=ssh  --permanent
firewall-cmd --zone=red-interna --add-port=6443/tcp  --permanent
firewall-cmd --zone=red-interna --add-port=10250/tcp  --permanent
firewall-cmd --reload
```

### **Configuración clave ssh**

Para securizar todavia más el servidor, voy a registrar mi clave publica ssh para el usuario root y voy a deshabilitar la autentificación por contraseña. Para ello creo el directorio .ssh en el directorio home de root y dentro creo un archivo llamado authorized_keys donde copiare la clave.

```console
mkdir -p ~/.ssh && touch ~/.ssh/authorized_keys && chmod -R go= ~/.ssh
```

Una vez hecho esto y copiada la clave dentro del archivo, cambio la siguiente configuración del servicio ssh en "/etc/ssh/sshd_config" para desactivar el loggin con contraseña en texto plano,

```nano
# To disable tunneled clear text passwords, change to no here!
PasswordAuthentication no
#PermitEmptyPasswords no
```

Hecho esto ya solo podre acceder al servidor con una clave ssh.

---

## Instalación

A continuación voy a describir el proceso de instalación de manera manual de Rancher desde un SO recien instalado.

### **Docker**
Lo primero que hay que instalar en el servidor sera docker, para ello tengo que **añadir el repositorio** correspondiente de docker para rocky linux, no hay un repositorio especifico de docker para rocky linux, pero al estar basado en centos, es compatible con el repositorio de este.

```console
dnf check-update
dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

(Ubuntu)

```console
apt update
apt install -y apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
apt update
```

Una vez añadido el repositorio, antes de instalar docker, hay que tener en cuenta que la version que estoy usando de rke no soporta versiones de docker superiores a la 20.10.x, asi que tengo que instalar esa versión desde el repositorio, para ver las versiones disponibles en el repositorio que acabo de instalar uso el siguiente comando:

```console
yum list docker-ce --showduplicates | sort -r
```

(Ubuntu)

```console
apt list docker-ce -a
```

El numero de versión sera el de la segunda columna empezando este justo despues de el doble punto ":" y terminando antes del guión "-", con lo cual la version que tengo que instalar sera la 20.10.24

Ahora que ya tengo añadido el repositorio y se que versión tengo que instalar, hay que instalarlo e iniciar el servicio.

```console
yum install -y docker-ce-20.10.24 docker-ce-cli-20.10.24 containerd.io
systemctl start docker
systemctl enable docker
```

(ubuntu)

```console
apt install -y docker-ce=5:20.10.24~3-0~ubuntu-jammy docker-ce-cli=5:20.10.24~3-0~ubuntu-jammy containerd.io
```

Compruebo que se ha iniciado correctamente

```console
systemctl status docker
```

```
● docker.service - Docker Application Container Engine
     Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; vendor preset: disabled)
     Active: active (running) since Thu 2023-04-20 12:34:24 CEST; 2min 56s ago
TriggeredBy: ● docker.socket
       Docs: https://docs.docker.com
   Main PID: 13170 (dockerd)
      Tasks: 8
     Memory: 39.5M
        CPU: 170ms
     CGroup: /system.slice/docker.service
             └─13170 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
...
```

Por último preparo un usuario que no sea root pero con permisos para ejecutar comandos de docker para utilizarlo como usuario de rancher y el cluster de kubernetes. Para ello creo el usuario y lo añado al grupo "docker"

```console
adduser rancher
usermod -aG docker rancher
```

### **Cluster de kubernetes (RKE)**
Ahora que tengo docker instalado tengo que instalar el cluster de kubernetes, he elegido instalar **RKE (Rancher Kubernetes Engine)** porque es la distribución de kubernetes creada por el equipo de rancher y es la recomendada para el mismo, aunque se puede instalar sobre cualquier otra distribución de cluster de kubernetes.

Lo primero sera descargar el fichero binario apropiado de rke, en este caso es el último release (1.4.4) en su version de linux_amd ([Releases de RKE](https://github.com/rancher/rke/releases)).
A partir de ahora, una vez cambio a el usuario "rancher" ejecutare todos los comandos siguientes desde ese usuario.

```console
dnf install -y wget
su - rancher
wget https://github.com/rancher/rke/releases/download/v1.4.4/rke_linux-amd64
mkdir -p .local/bin && mv rke_linux-amd64 .local/bin/rke && chmod +x .local/bin/rke
```

(Ubuntu)

```console
su - rancher
wget https://github.com/rancher/rke/releases/download/v1.4.4/rke_linux-amd64
mkdir -p .local/bin && mv rke_linux-amd64 .local/bin/rke && chmod +x .local/bin/
echo 'export PATH="~/.local/bin:$PATH"' >> .bashrc
export PATH="~/.local/bin:$PATH"
rke
```
A continuación tengo que preparar la **configuración del cluster** dentro de un archivo llamado "cluster.yml", voy a usar el [ejemplo de configuración minima](https://rke.docs.rancher.com/example-yamls#minimal-cluster-yml-example) que proporciona rke y lo modifico para que se adecue a mi servidor.

```yml
nodes:
    - address: 192.168.56.101
      user: rancher
      role:
        - controlplane
        - etcd
        - worker
      ssh_key_path: /home/rancher/.ssh/id_rsa
```
Antes de instalar el cluster de rke debo crear una **clave ssh** para el usuario "rancher" de la misma forma que lo hice para root, para crearla simplemente uso el comando **"ssh-keygen"** con el usuario rancher y luego añado la clave publica generada en el fichero **.shh/authorized_keys**

Ahora para crear el cluster de kubernetes debo ejecutar el siguiente comando mientras me encuentro en el mismo directorio en el que esta el fichero "cluster.yml"

```console
rke up
```

Si todo ha ido correctamente se vera la siguiente linea al final del proceso:

```console
INFO[0187] Finished building Kubernetes cluster successfully
```

Por último, en la documetación de RKE recomiendan hacer copias de los tres archivos que hemos generado durante la instalación **(cluster.rkestate  cluster.yml  kube_config_cluster.yml)** ya que en estos ficheros tenemos toda la configuración y información de acceso del cluster. Ahora que ya tengo instalado y funcionando el cluster de RKE, hay que instalar las herramientas necesarias para administrarlo e instalar rancher las cuales son **kubectl** y **helm**. No hace falta que estas herramientas se instalen en el propio servidor de kubernetes ya que se puede administrar de manera remota, pero yo las voy a instalar directamente en el servidor.

### **Instalar Kubectl y Helm**

Primero voy a **instalar y configurar kubectl**, para ello primero tengo que elegir una versión y descargarla en el servidor con el comando "curl", para elegir una versión hay que tener en cuenta que kubectl funciona con una versión inferior o superior de kubernetes que la suya, es decir, si instalamos kubectl 1.26, este nos valdra para administrar clusters con kubernetes 1.25.x, 1.26.x o 1.27.x

En el caso del cluster que he instalado tiene la versión 1.25, con lo cual debo instalar la versión 1.26 de kubectl. Ejecutando los siguientes comandos ya tendre descargado e instalado kubectl.

```console
curl -LO https://dl.k8s.io/release/v1.26.0/bin/linux/amd64/kubectl
chmod +x kubectl
mkdir -p ~/.local/bin
mv ./kubectl ~/.local/bin/kubectl
```

Ahora tendremos que copiar el archivo de conexión del cluster que se genero durante la instalación a el directorio de configuración de kubectl.

```console
mkdir ~/.kube
cp ~/rke/kube_config_cluster.yml ~/.kube/config
```

Ya tenemos configurado kubectl, ahora si ejecuto un **"kubectl get nodes"** deberia de ver el nodo local

```console
NAME             STATUS   ROLES                      AGE   VERSION
192.168.56.101   Ready    controlplane,etcd,worker   84m   v1.25.6
```

Ahora voy a instalar **Helm**

```
wget https://get.helm.sh/helm-v3.11.3-linux-amd64.tar.gz
tar -zxvf helm-v3.11.3-linux-amd64.tar.gz
mv linux-amd64/helm .local/bin/helm
```

```
helm install rancher rancher-stable/rancher \
  --namespace cattle-system \
  --set hostname=pmoldenhauer.trevenque.es \
  --set bootstrapPassword=momoEFP \
  --set ingress.tls.source=letsEncrypt \
  --set letsEncrypt.email=pmoldenhauer@trevenque.es \
  --set letsEncrypt.ingress.class=nginx
```