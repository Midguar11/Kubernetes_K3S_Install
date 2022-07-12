# kubernetes k3s install with script:

- references: https://rancher.com/docs/k3s/latest/en/quick-start/

- connect to master ssh

      sudo apt-get update 
      sudo apt-get install -y apt-transport-https
      sudo apt install docker.io
      sudo systemctl start docker
      sudo systemctl enable docker
      sudo apt-get install -y curl
  
# Setup router

open port in router: yourkubermasterserverip:6443

# Master

      curl -sfL https://get.k3s.io | sh -
      /var/lib/rancher/k3s/server/node-token
     
# worker1-2:

- Connect to worker ssh

      curl -sfL https://get.k3s.io | K3S_URL=https://192.168.0.124:6443 K3S_TOKEN=youtokennumberhere sh -

# setup kube before use jenkins


    kind create cluster --config jenkins-config.yaml
    kubectl cluster-info --context kind-kind
    kubectl create namespace jenkins
    kubectl create serviceaccount jenkins --namespace=jenkins
    kubectl describe secret $(kubectl describe serviceaccount jenkins --namespace=jenkins | grep Token | awk '{print $2}') --namespace=jenkins
    kubectl create rolebinding jenkins-admin-binding --clusterrole=admin --serviceaccount=jenkins:jenkins --namespace=jenkins
    echo "K3S_KUBECONFIG_MODE=\"644\"" >> /etc/systemd/system/k3s.service.env

#  Install Nginx

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
- Jenkins user add admin role "sudo kubectl create rolebinding jenkins-admin-binding --clusterrole=admin --serviceaccount=jenkins:jenkins --namespace=jenkins "

# References

Thx for: 

https://www.youtube.com/watch?v=ZXaorni-icg&t=295s

https://www.youtube.com/watch?v=1hwGdey7iUU
