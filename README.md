# Kubernetes K3S telepítés

( Ez müködik szoló modban is tehat, hogy csak egy Master van a Workerkek számá te döntöd el. Ez a kubernetesnek egy könnyitet verziója de rendelkezik minden fontosabb képességel amivel egy k8s )

# Referenciák:
 
- https://rancher.com/docs/k3s/latest/en/quick-start/
- https://www.youtube.com/watch?v=1hwGdey7iUU
- https://gitlab.com/cloud-versity/rancher-k3s-first-steps

# Előfeltételek

1 szerver Ubuntu Linux grafikus felülettel ( KubeMaster, control plane )
1 szerver Ubuntu grafikus felület nélküli ( KubeWorker )

# Csatlakozz a kube masterhez akár SSH val is, legjobb ha Routkent adod ki ezeket a parancsokat

      sudo apt-get update 
      sudo apt-get upgrade
      sudo apt-get install -y apt-transport-https
      sudo apt install docker.io
      sudo systemctl start docker
      sudo systemctl enable docker
      sudo apt-get install -y curl
      sudo apt install xrdp -y
      sudo systemctl status xrdp
      sudo systemctl start xrdp
      
  
# Állítsd be a tüzfalat

      ufw enable
      ufw allow 22/tcp && ufw allow 80/tcp && ufw allow 443/tcp && ufw allow 2376:2380/tcp && ufw allow 6443/tcp && ufw allow 8472/udp && ufw allow 9099/tcp && ufw allow 10250/tcp && ufw allow 10254/tcp && ufw allow 8472/udp && ufw allow 30000:32767/tcp && ufw allow 30000:32767/udp && ufw allow 9500/tcp && ufw allow 1883/tcp && ufw allow 15672/tcp
      
      

# Ha nem a traefikot akarod Ingress vezérlőnek hanem mást akkor rakd bele ezt a parancsba "--disable traefik"

     curl -sfL https://get.k3s.io | sh -s - --write-kubeconfig-mode 644 --node-name KornyezetKubeMaster01
     
# Kube config beállítása
 
     export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
     kubectl config view --raw > ~/.kube/config
   
# Lens használata
 Amennyiben lenszet használsz majd a kezeléshez akkor kréd le a config tartalmát arra majd szükséged lesz. Azt masold ki magadnak egy text filba
 
    cat /etc/rancher/k3s/k3s.yaml
    
# Ellnörizd a Cluster állapotát

       kubectl get nodes
   
# kérdi ki a kubemaster tokent

     cat /var/lib/rancher/k3s/server/node-token
     
#  Worker Telepításe
 Kapcsolodj sshval a workerhez, ha több worker van akkor kapcsolodj mindhez egyesével és mindegyiken végezd el ezt
 A curls parancsban helyetesítsd be a master gép IP jét, a token helyére másold be a kubemaster tokenjét
 
    sudo apt-get update 
    sudo apt-get upgrade
    curl -sfL https://get.k3s.io | K3S_NODE_NAME=KörnyezetneveWorker01 K3S_URL=https://<your_master_server_ip>:6443 K3S_TOKEN=<TOKEN> sh - 
 
# Állítsd be a tüzfalat

      ufw enable
      ufw allow 22/tcp && ufw allow 80/tcp && ufw allow 443/tcp && ufw allow 2376:2380/tcp && ufw allow 6443/tcp && ufw allow 8472/udp && ufw allow 9099/tcp && ufw allow 10250/tcp && ufw allow 10254/tcp && ufw allow 8472/udp && ufw allow 30000:32767/tcp && ufw allow 30000:32767/udp && ufw allow 9500/tcp && ufw allow 1883/tcp && ufw allow 15672/tcp

# Ellnörizd a Cluster állapotát

       kubectl get nodes
       
# Kubernetis Dashboard k3s

https://docs.k3s.io/installation/kube-dashboard


# setup kube before use jenkins


    kind create cluster --config jenkins-config.yaml
    kubectl cluster-info --context kind-kind
    kubectl create namespace jenkins
    kubectl create serviceaccount jenkins --namespace=jenkins
    kubectl describe secret $(kubectl describe serviceaccount jenkins --namespace=jenkins | grep Token | awk '{print $2}') --namespace=jenkins
    kubectl create rolebinding jenkins-admin-binding --clusterrole=admin --serviceaccount=jenkins:jenkins --namespace=jenkins
    echo "K3S_KUBECONFIG_MODE=\"644\"" >> /etc/systemd/system/k3s.service.env



#  Install Nginx
 *if you disable treafik ingress controll
 
- connect kube master

    kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.2.1/deploy/static/provider/cloud/deploy.yaml
    kubectl get ns
    kubectl -n ingress-nginx get po

# Try new pipeline job

pipeline {
  agent {
    kubernetes {
      yaml '''
        apiVersion: v1
        kind: Pod
        spec:
          containers:
          - name: maven
            image: maven:alpine
            command:
            - cat
            tty: true
        '''
    }
  }
  stages {
    stage('Run maven') {
      steps {
        container('maven') {
          sh 'mvn -version'
        }
      }
    }
  }
}

# Try new pipeline job 2

pipeline {
  agent {
    kubernetes {
      yaml '''
        apiVersion: v1
        kind: Pod
        spec:
          containers:
          - name: maven
            image: maven:alpine
            command:
            - cat
            tty: true
          - name: node
            image: node:16-alpine3.12
            command:
            - cat
            tty: true
        '''
    }
  }
  stages {
    stage('Run maven') {
      steps {
        container('maven') {
          sh 'mvn -version'
        }
        container('node') {
          sh 'npm version'
        }
      }
    }
  }
}

# Command:

- tehese are command on use kuber master server
- Show node status: " k3s kubectl get nodes "
- Cluster data: " cat /etc/rancher/k3s/k3s.yaml "
- runing status kubernetes. " systemctl status k3s "
- token: " cat /var/lib/rancher/k3s/server/node-token "
- certificate do you need a jenkins crendetional: " sudo cat /etc/rancher/k3s/k3s.yaml "
- check deployed content " sudo kubectl get deployment "
- " sudo kubectl get pods "
- cluster ip and more " sudo kubectl get svc  "
- control, core dns, metric ip and name " sudo kubectl cluster-info "
- create namespace " sudo kubectl create namespace jenkins "
- list of name space " sudo kubectl get ns "
- cretae serviceuser is name Jenkins in jenkins namespace " kubectl create serviceaccount jenkins --namespace=jenkins "
- create sercret "sudo kubectl describe secret $(kubectl describe serviceaccount jenkins --namespace=jenkins | grep Token | awk '{print $2}') --namespace=jenkins "
- Jenkins user add admin role "sudo kubectl create rolebinding jenkins-admin-binding --clusterrole=admin 
- more info pods : " kubectl describe pod "

# References

Thx for: 

https://www.youtube.com/watch?v=ZXaorni-icg&t=295s

https://www.youtube.com/watch?v=1hwGdey7iUU

# Kubermaster config ssh

  - nano /etc/ssh/sshd_config

  UsePAM no
  PasswordAuthentication no

  restart

# Firewall setup Master and worker


    ufw allow 22/tcp && ufw allow 80/tcp && ufw allow 443/tcp && ufw allow 2376:2380/tcp && ufw allow 6443/tcp && ufw allow 8472/udp && ufw allow 9099/tcp && ufw allow 10250/tcp && ufw allow 10254/tcp && ufw allow 8472/udp && ufw allow 30000:32767/tcp && ufw allow 30000:32767/udp && ufw allow 9500/tcp && ufw allow 1883/tcp && ufw allow 15672/tcp

