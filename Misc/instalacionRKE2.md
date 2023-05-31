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

