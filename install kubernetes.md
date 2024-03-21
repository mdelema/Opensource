# Cómo instalar Kubernetes en Ubuntu 22.04

### Instale Docker en cada nodo del servidor ejecutando los pasos a continuación:

1. Actualice la lista de paquetes:
'''
sudo apt update
'''
2. Instale Docker con el siguiente comando:
'''
sudo apt install docker.io -y
'''
3. Configure Docker para que se inicie al arrancar ingresando:
'''
sudo systemctl enable docker
'''
4. Verifique que Docker se esté ejecutando:
'''
sudo systemctl status docker
'''
5. Si Docker no se está ejecutando, inícielo con el siguiente comando:
'''
sudo systemctl start docker
'''

### Instalar Kubernetes
Configurar Kubernetes en un sistema Ubuntu implica agregar el repositorio de Kubernetes a la lista de fuentes de APT e instalar las herramientas relevantes. 

Paso 1: Agregar la clave de firma de Kubernetes
Dado que Kubernetes proviene de un repositorio no estándar, descargue la clave de firma para asegurarse de que el software sea auténtico.
'''
curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes.gpg
'''
Paso 2: Agregar repositorios de software
'''
echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/kubernetes.gpg] http://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list
'''
Asegúrese de que todos los paquetes estén actualizados:
'''
sudo apt update
'''
Paso 3: Instalar las herramientas de Kubernetes
- Kubeadm . Una herramienta que inicializa un clúster de Kubernetes acelerando la configuración utilizando  las mejores prácticas de la comunidad .
- Kubelet . El paquete de trabajo que se ejecuta en cada nodo e inicia contenedores. La herramienta le brinda acceso de línea de comandos a los clústeres.
- Kubectl . La interfaz de línea de comandos para interactuar con clústeres.

a. Ejecute el comando de instalación :
'''
sudo apt install kubeadm kubelet kubectl
'''
Instalación de herramientas de Kubernetes.
b. Marque los paquetes como retenidos para evitar la instalación, actualización o eliminación automática:
'''
sudo apt-mark hold kubeadm kubelet kubectl
'''
Poner las herramientas de Kubernetes en espera.
c. Verifique la instalación con:
'''
kubeadm version
'''

### Implementar Kubernetes

Paso 1: Prepárese para la implementación de Kubernetes
a. Deshabilite todos los espacios de intercambio con el  comando swapoff :
'''
sudo swapoff -a
'''
Luego use el comando sed  a continuación para realizar los ajustes necesarios en el archivo /etc/fstab :
'''
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
'''
b. Cargue los módulos en contenedor necesarios . Comience abriendo el archivo de configuración contenedor en un editor de texto , como nano :
'''
sudo nano /etc/modules-load.d/containerd.conf
'''
c. Agregue las dos líneas siguientes al archivo:
'''
overlay
br_netfilter
'''
Edición de configuración en contenedor.
Guarda el archivo y cierra.
d. A continuación, utilice el  comando modprobe  para agregar los módulos:
'''
sudo modprobe overlay
sudo modprobe br_netfilter
'''
e. Abra el  archivo kubernetes.conf  para configurar la red de Kubernetes:
'''
sudo nano /etc/sysctl.d/kubernetes.conf
'''
f. Agregue las siguientes líneas al archivo:
'''
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
'''
Editando la configuración de Kubernetes.
Guarda el archivo y cierra.

g. Vuelva a cargar la configuración escribiendo:
'''
sudo sysctl --system
'''
Recarga de configuración del sistema.

Paso 2: Asigne un nombre de host único para cada nodo del servidor
1. Decida qué servidor será el nodo maestro. Luego, ingrese el comando en ese nodo para nombrarlo en consecuencia:
'''
sudo hostnamectl set-hostname master-node
'''
2. A continuación, establezca el nombre de host en el primer nodo trabajador ingresando el siguiente comando:
'''
sudo hostnamectl set-hostname worker01
'''
3. Edite el archivo de hosts en cada nodo agregando las direcciones IP y los nombres de host de los servidores que formarán parte del clúster.

Editando el archivo de hosts.
4. Reinicie la aplicación de terminal para aplicar el cambio de nombre de host.

Paso 3: Inicializar Kubernetes en Master Node
Una vez que termine de configurar los nombres de host en los nodos del clúster, cambie al nodo maestro y siga los pasos para inicializar Kubernetes en él:

1. Abra el archivo kubelet en un editor de texto.
'''
sudo nano /etc/default/kubelet
'''
2. Agregue la siguiente línea al archivo:
'''
KUBELET_EXTRA_ARGS="--cgroup-driver=cgroupfs"
'''
Guardar y Salir.

3. Vuelva a cargar la configuración y reinicie kubelet:
'''
sudo systemctl daemon-reload && sudo systemctl restart kubelet
'''
4. Abra el archivo de configuración del demonio Docker:
'''
sudo nano /etc/docker/daemon.json
'''
5. Agregue el siguiente bloque de configuración:
'''
    {
      "exec-opts": ["native.cgroupdriver=systemd"],
      "log-driver": "json-file",
      "log-opts": {
      "max-size": "100m"
   },

       "storage-driver": "overlay2"
       }
'''
Editando el archivo daemon.json.
Guarda el archivo y cierra.

6. Vuelva a cargar la configuración y reinicie Docker:
'''
sudo systemctl daemon-reload && sudo systemctl restart docker
'''
7. Abra el  archivo de configuración de kubeadm  :
'''
sudo nano /etc/systemd/system/kubelet.service.d/10-kubeadm.conf
'''
8. Agregue la siguiente línea al archivo:
'''
Environment="KUBELET_EXTRA_ARGS=--fail-swap-on=false"
'''
Configuración de Kubeadm.
Guarda el archivo y cierra.

9. Vuelva a cargar la configuración y reinicie kubelet:
'''
sudo systemctl daemon-reload && sudo systemctl restart kubelet
'''
10. Finalmente, inicialice el clúster escribiendo:
'''
sudo kubeadm init --control-plane-endpoint=master-node --upload-certs
'''
Una vez que finaliza la operación, el resultado muestra un  kubeadm join comando en la parte inferior. Tome nota de este comando, ya que lo utilizará para unir los nodos trabajadores al clúster.

El nodo maestro se une al clúster.
11. Cree un directorio para el clúster de Kubernetes:
'''
mkdir -p $HOME/.kube
'''
12. Copie el archivo de configuración al directorio:
'''
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
'''
13. Cambie la propiedad del directorio al usuario y grupo actual usando el comando chown :
'''
sudo chown $(id -u):$(id -g) $HOME/.kube/config
'''
Paso 4: implementar la red de pods en el clúster
Una red de pods es una forma de permitir la comunicación entre diferentes nodos del clúster. Este tutorial utiliza el  administrador de red de nodos de franela para crear una red de pods.


1. Utilice kubectl para instalar franela:
'''
kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
'''
2. Desinfectar el nodo:
'''
kubectl taint nodes --all node-role.kubernetes.io/control-plane-
'''
Paso 5: unir el nodo trabajador al clúster
Repita los siguientes pasos en cada nodo trabajador para crear un clúster:

1. Detenga y desactive AppArmor :
'''
sudo systemctl stop apparmor && sudo systemctl disable apparmor
'''
2. Reinicie el contenedor :
'''
sudo systemctl restart containerd.service
'''
3. Aplique el   comando kubeadm join del Paso 3 en los nodos trabajadores para conectarlos al nodo maestro. Anteponga el comando con sudo:
'''
sudo kubeadm join [master-node-ip]:6443 --token [token] --discovery-token-ca-cert-hash sha256:[hash]
'''
El nodo trabajador se une al clúster.
Reemplace [master-node-ip] , [token] y [hash] con los valores de la salida del comando kubeadm join .

4. Después de unos minutos, cambie al servidor maestro e ingrese el siguiente comando para verificar el estado de los nodos:
'''
kubectl get nodes
'''
Visualización de nodos implementados.
