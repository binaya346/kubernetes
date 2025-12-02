# Prerequisite
Add worker & master node ip & ssh creds in inventory/hosts.ini file. 

# Install ansible
python3 -m venv venv
source venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt

# Install ansible Dependencies for kubernetes

ansible-galaxy role install geerlingguy.containerd
ansible-galaxy collection install ansible.posix

# Test Ansible connectivity
ansible all -i inventory/hosts.ini -m ping

# Docker & docker compose setup
ansible-playbook -i inventory/hosts.ini playbooks/docker-setup.yml
```bash
This playbook performs the following:

  Updates package cache
  Installs Docker repository dependencies
  Adds Docker’s official GPG key
  Adds Docker APT repository
  Installs Docker CE, containerd, and Compose plugin
  Adds user to the docker group (no sudo needed)
  Configures logging (rotating logs)
  Starts and enables Docker service
  Installs Docker Compose standalone binary
  Runs a validation test (hello-world)

```

# Run the Kubernetes Setup Playbook
ansible-playbook -i inventory/hosts.ini playbooks/k8s-setup.yml

```bash
# Check Node Status
kubectl get nodes -o wide

# Expected output:
# NAME     STATUS   ROLES           AGE   VERSION
# master   Ready    control-plane   XXm   v1.xx.x
# worker1  Ready    <none>          XXm   v1.xx.x

# Check System Pods
kubectl get pods -n kube-system

# Expected output
#Kubernetes control plane is running at https://192.168.130.148:6443
#CoreDNS is running at https://192.168.130.148:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

# Check Join Tokens
sudo kubeadm token list

# Regenerate Join Command
sudo kubeadm token create --print-join-command

# View Cluster Events
kubectl get events -A

```

# Deploy laravel application
ansible-playbook -i inventory/hosts.ini playbooks/deploy-laravel.yml

```bash
## ✅ Verification

### Check Pod Status
# View all pods
kubectl get pods -n laravel-app -o wide

# Expected output:
# NAME                       READY   STATUS    NODE
# laravel-xxxxxxxxx-xxxxx    2/2     Running   worker-1
# laravel-xxxxxxxxx-xxxxx    2/2     Running   worker-2
# mysql-0                    1/1     Running   master


### Check Services

kubectl get svc -n laravel-app

# Expected:
# NAME              TYPE       CLUSTER-IP      PORT(S)
# laravel-service   NodePort   10.96.x.x       80:30080/TCP
# mysql-service     ClusterIP  None            3306/TCP


### Access Application

# Get node IP
kubectl get nodes -o wide

### Check Logs

# Nginx logs
kubectl logs -l app=laravel -c nginx -n laravel-app --tail=50

# PHP-FPM logs
kubectl logs -l app=laravel -c php-fpm -n laravel-app --tail=50

# MySQL logs
kubectl logs mysql-0 -n laravel-app --tail=50

# Follow logs in real-time
kubectl logs -f deployment/laravel -c php-fpm -n laravel-app
```

# Setup monitoring
ansible-playbook -i inventory/hosts.ini playbooks/setup-monitoring.yml

# Encrypt the secrets using ansible vaults
ansible-vault encrypt ansible/vars/secrets.yml

# Edit the vault file
ansible-vault edit vars/secrets.yml (Ask Vault password to other devs)

# Ask vault password while applying
--ask-vault-pass 