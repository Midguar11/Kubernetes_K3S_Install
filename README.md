# Kubernetes K3S telepítés

( Ez müködik szoló modban is tehat, hogy csak egy Master van a Workerkek számá te döntöd el. Ez a kubernetesnek egy könnyitet verziója de rendelkezik minden fontosabb képességel amivel egy k8s )

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
      
  
# Állítsd be a tűzfalat

      ufw enable
      ufw allow 22/tcp && ufw allow 80/tcp && ufw allow 443/tcp && ufw allow 2376:2380/tcp && ufw allow 6443/tcp && ufw allow 8472/udp && ufw allow 9099/tcp && ufw allow 10250/tcp && ufw allow 10254/tcp && ufw allow 8472/udp && ufw allow 30000:32767/tcp && ufw allow 30000:32767/udp && ufw allow 9500/tcp && ufw allow 1883/tcp && ufw allow 15672/tcp
      
      

# Ha nem a traefikot akarod Ingress vezérlőnek hanem mást akkor rakd bele ezt a parancsba "--disable traefik"

     curl -sfL https://get.k3s.io | sh -s - --write-kubeconfig-mode 644 --node-name KornyezetKubeMaster01
     
# Kube config beállítása
 
     export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
     kubectl config view --raw > ~/.kube/config
   
# Lens használata
 Amennyiben lenszet használsz majd a kezeléshez akkor kréd le a config tartalmát arra majd szükséged lesz. Azt masold ki magadnak egy text file-ba
 
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
 
# Állítsd be a tűzfalat

      ufw enable
      ufw allow 22/tcp && ufw allow 80/tcp && ufw allow 443/tcp && ufw allow 2376:2380/tcp && ufw allow 6443/tcp && ufw allow 8472/udp && ufw allow 9099/tcp && ufw allow 10250/tcp && ufw allow 10254/tcp && ufw allow 8472/udp && ufw allow 30000:32767/tcp && ufw allow 30000:32767/udp && ufw allow 9500/tcp && ufw allow 1883/tcp && ufw allow 15672/tcp

# Ellnörizd a Cluster állapotát

    kubectl get nodes
    
# Lens használata
 A Lens egy olyan vezérlő és áttekintő felület ahol a kubernetes clustereidet rendszerben tudod kezelni és az áttekintést, karbantartást, modosításokat, hibakeresést, egyszerűvé és könnyebbé teszi. Szószerint napokat takarithatsz meg vele. Az egyik legjobb Kubernetes kezelő a piacon 
Innen lehet letölteni, mivel ez egy asztali alkalmazás válaszd ki a neked megfelelő op rendszert. https://k8slens.dev/  
Telepítás után a cluster hozzadásánál találsz egy részt ahol yamlként tudod hozzáadni a clustered configját, ide másold be a fent leírt módon elmentett config text file-d tatalmát és a benne lévő IP-címet cseréld le a master géped IP-jére. Ha elmented és jól csináltad már csak csatlakoznod kell.
      


# Referenciák:
 
- https://rancher.com/docs/k3s/latest/en/quick-start/
- https://www.youtube.com/watch?v=1hwGdey7iUU
- https://gitlab.com/cloud-versity/rancher-k3s-first-steps

