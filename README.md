# kubernetes k3s install with script:

- references: https://rancher.com/docs/k3s/latest/en/quick-start/

- connect to master ssh

      sudo apt-get update 
      sudo apt-get install -y apt-transport-https
      sudo apt install docker.io
      sudo systemctl start docker
      sudo systemctl enable docker
      sudo apt-get install -y curl
  
# Master

      curl -sfL https://get.k3s.io | sh -
      /var/lib/rancher/k3s/server/node-token
     
# worker1-2:

      curl -sfL https://get.k3s.io | K3S_URL=https://192.168.0.124:6443 K3S_TOKEN=K10edaa3e53481877fc240c79aa0b7b471a335e04fef3327985603c698d5a5f18c0::server:a2e743e0fdf9d8b6fbbbf3845c39e829 sh -

# Command:

- Show node status: " k3s kubectl get nodes "
- Cluster data: " cat /etc/rancher/k3s/k3s.yaml "
- runing status kubernetes. " systemctl status k3s "
- token: " cat /var/lib/rancher/k3s/server/node-token "