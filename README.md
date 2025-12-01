# Install python & pip
brew install python

# Install ansible
python3 -m venv ansible-venv
python3.12 -m venv ansible-venv
source ansible-venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt

# Dependencies

ansible-galaxy role install geerlingguy.containerd

# Test Ansible connectivity
ansible all -i inventory/hosts.ini -m ping


# Run the Kubernetes Setup Playbook
ansible-playbook -i inventory/hosts.ini playbooks/k8s-setup.yml


# INstall python dependencies in master & worker node
apt update
apt install -y python3 python3-pip python3-setuptools python3-distutils python3-six

pip3 install --upgrade pip




Remote server:
sudo apt update
sudo apt install -y python3-full python3-venv
python3 -m venv /opt/ansible-venv
source /opt/ansible-venv/bin/activate

pip install --upgrade pip
pip install six
pip install jmespath
pip install cryptography
pip install setuptools
pip install wheel


ansible-playbook -i inventory/hosts.ini playbooks/deploy-laravel.yml


# PUsh the locally build repo into docker hub
docker buildx build --platform=linux/amd64 -t rijalbinaya2/laravel-app:latest .


docker push rijalbinaya2/laravel-app:latest

# restart deployment
kubectl rollout restart deployment/laravel-app -n laravel-app

# Check pod status
kubectl get pods -n laravel-app

# Delete pods to force re-pull
kubectl delete pods -l app=laravel -n laravel-app

# Watch them restart
kubectl get pods -n laravel-app -w

# Run monitoring setup
ansible-playbook -i inventory/hosts.ini playbooks/setup-monitoring.yml

# check pods log
kubectl logs laravel-55cf9469df-9jsb4 -n laravel-app

# describe pod
kubectl describe pod laravel-55ffb4b9f6-m4v94 -n laravel-app

# add route

sudo route add -net 192.168.130.0/24 192.168.200.105