### **Cluster de kubernetes (RKE2)**
Ahora que tengo docker instalado tengo que instalar el cluster de kubernetes, he elegido instalar **RKE2 (Rancher Kubernetes Engine)** porque es la distribución de kubernetes creada por el equipo de rancher y es la recomendada para el mismo, aunque se puede instalar sobre cualquier otra distribución de cluster de kubernetes.

Para instalarlo lo primero sera descargar y ejecutar desde el **usuario root** el siguiente script:

```console
curl -sfL https://get.rke2.io | sh -
yum -y install rke2-server
```

(Ubuntu)

```console
curl -sfL https://get.rke2.io | sh -
```

Ahora ya tenemos disponible el comando **"rke2"** y podremos iniciar el servidor con los siguientes comandos:
```console
systemctl enable rke2-server
systemctl start rke2-server
```

Antes de iniciar el servidor, hay que crear un archivo de configuracion y cambiar las opciones que sean necesarias.

```console
mkdir -p /etc/rancher/rke2
touch /etc/rancher/rke2/config.yaml
```

```yaml
write-kubeconfig-mode: "0644"
tls-san:
  - "pmoldenahuer.trevenque.es"
node-label:
  - "type=master"
cluster-cidr:
  - "192.168.1.0/24"
service-cidr:
  - "192.168.2.0/24"
cluster-dns:
  - "192.168.2.10"
cluster-domain:
  - "trevenque.es"
debug: false
```

Comprobamos que esta funcionando correctamente:

```console
systemctl status rke2-server
```

Se crea automaticamente un archivo de configuración de kubectl en el directorio **/etc/rancher/rke2/rke2.yaml**.

Ya tengo el servidor de rke2 activo, ahora tengo que instalar las herramientas necesarias para administrarlo.

### **Instalar Kubectl y Helm**
A partir de aqui hago todas las instalaciones con el usuario **rancher**.

Primero voy a **instalar y configurar kubectl**, para ello primero tengo que elegir una versión y descargarla en el servidor con el comando "curl", para elegir una versión hay que tener en cuenta que kubectl funciona con una versión inferior o superior de kubernetes que la suya, es decir, si instalamos kubectl 1.26, este nos valdra para administrar clusters con kubernetes 1.25.x, 1.26.x o 1.27.x

En el caso del cluster que he instalado tiene la versión 1.24, con lo cual debo instalar la versión 1.25 de kubectl. Ejecutando los siguientes comandos ya tendre descargado e instalado kubectl.

```console
curl -LO https://dl.k8s.io/release/v1.25.0/bin/linux/amd64/kubectl
chmod +x kubectl
mkdir -p ~/.local/bin
mv ./kubectl ~/.local/bin/kubectl
```

Ahora tendremos que copiar el archivo de conexión del cluster que se genero durante la instalación a el directorio de configuración de kubectl. Lo tendremos que hacer con el usuario **root** ya que con rancher no tenemos permisos para copiarlo.

```console
mkdir ~/.kube
su -
cp /etc/rancher/rke2/rke2.yaml /home/rancher/.kube/config
chown rancher:rancher /home/rancher/.kube/config
su - rancher
```

Ya tenemos configurado kubectl, ahora si ejecuto un **"kubectl get nodes"** deberia de ver el nodo local

```console
NAME           STATUS   ROLES                       AGE   VERSION
pmoldenhauer   Ready    control-plane,etcd,master   63m   v1.24.12+rke2r1
```

Ahora voy a instalar **Helm**, para ello, lo unico que tengo que hacer es descargarme el fichero binario de la última versión de Helm de la [pagina oficial](https://github.com/helm/helm/releases), descomprimirla, moverla a algun directorio que este añadido en $PATH, en este caso lo voy a poner en ".local/bin/helm" y cambiarle el nombre al fichero a "helm".

```cosole
wget https://get.helm.sh/helm-v3.11.3-linux-amd64.tar.gz
tar -zxvf helm-v3.11.3-linux-amd64.tar.gz
mv linux-amd64/helm .local/bin/helm
```

Hay muchas otras maneras de instalar helm, pero esta me ha parecido la más sencilla, sobre todo para más tarde hacerlo de manera automatizada. Ahora que tengo kubectl y helm listos, solo me queda instalar **cert-manager** y rancher.