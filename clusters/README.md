# Create Project Directory
```
pip install ruamel.yaml
mkdir -p homelab/kubernetes
cd homelab/kubernetes
```


# Clone the kubespray Project
```
git clone https://github.com/kubernetes-sigs/kubespray.git
```


# Install and activate python virtual environment
```
python3 -m venv kubespray-venv
source kubespray-venv/bin/activate
```

# Install ansible 
```
cd kubespray
pip install -U -r requirements.txt
```

# Create hosts inventory
```
declare -a IPS=(172.16.102.7 172.16.102.8 172.16.102.9 172.16.102.10)
cd ../ 
mkdir -p cluster/homelab-k8s
CONFIG_FILE=cluster/homelab-k8s/hosts.yml python3 kubespray/contrib/inventory_builder/inventory.py ${IPS[@]}
```

# Inspect hosts.yaml
```
cat cluster/homelab-k8s/hosts.yml
```

# Deploy cluster
```
cd kubespray
ansible-playbook -i ../cluster/homelab-k8s/hosts.yml -e @../cluster/homelab-k8s/cluster-config.yml --user=debian --become --become-user=root cluster.yml
```

# Verify deployment
```
ssh root@172.16.102.7
sudo -i 
kubectl get nodes
kubectl -n kube-system get pods
less /etc/kubernetes/admin.conf 
```

# Upgrade Cluster
```
ansible-playbook -i ../cluster/homelab-k8s/hosts.yml -e @../cluster/homelab-k8s/cluster-config.yml --user=debian --become --become-user=root upgrade-cluster.yml
```

# Scale Down Cluster
```
ansible-playbook -i ../cluster/homelab-k8s/hosts.yml -e @../cluster/homelab-k8s/cluster-config.yml --user=root --become --become-user=root remove-node.yml -e node=node5
```


# Scale Up cluster
```
ansible-playbook -i ../cluster/homelab-k8s/hosts.yml -e @../cluster/homelab-k8s/cluster-config.yml --user=root --become --become-user=root scale.yml --limit=node5
```
