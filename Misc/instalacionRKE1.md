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