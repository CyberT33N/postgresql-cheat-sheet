# postgresql-cheat-sheet


# GUI

## pgadmin
- https://www.pgadmin.org/


### Windows
- https://www.pgadmin.org/download/pgadmin-4-windows/


### Linux

```shell
#
# Setup the repository
#

# Install the public key for the repository (if not done previously):
curl -fsS https://www.pgadmin.org/static/packages_pgadmin_org.pub | sudo gpg --dearmor -o /usr/share/keyrings/packages-pgadmin-org.gpg

# Create the repository configuration file:
sudo sh -c 'echo "deb [signed-by=/usr/share/keyrings/packages-pgadmin-org.gpg] https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/$(lsb_release -cs) pgadmin4 main" > /etc/apt/sources.list.d/pgadmin4.list && apt update'

#
# Install pgAdmin
#

# Install for both desktop and web modes:
sudo apt install pgadmin4

# Install for desktop mode only:
sudo apt install pgadmin4-desktop

# Install for web mode only: 
sudo apt install pgadmin4-web 

# Configure the webserver, if you installed pgadmin4-web:
sudo /usr/pgadmin4/bin/setup-web.sh
```










<br><br>
<br><br>
___
<br><br>
<br><br>

# Helm Chart

## Minikube
- https://github.com/CyberT33N/minikube/tree/main/postgresql
  
custom-values.yaml
```
auth:
  enablePostgresUser: true
  # Superuser password
  postgresPassword: "test"
  username: "test"
  password: "test"
  database: "test"

primary:
  service:
    type: NodePort
    nodePorts:
      postgresql: 30543

  persistence:
    enabled: true
    size: 8Gi

  resources:
    requests:
      memory: "256Mi"
      cpu: "250m"
    limits:
      memory: "512Mi"
      cpu: "500m"
```



<details>
<summary>Click to expand..</summary>

### 🔗 Connection String
- `postgresql://test:test@192.168.49.2.nip.io:30543/test`

### 📥 Add Repo
```shell
# Add Bitnami repo
helm repo add bitnami https://charts.bitnami.com/bitnami

# Update Helm repo
helm repo update

# List available Helm Chart versions
helm search repo bitnami/postgresql --versions
```

### 📦 Install Helm Chart
```shell
# This will download the PostgreSQL Helm chart to the folder ./postgresql/Chart
cd ~/Projects/minikube
mkdir -p ./postgresql/Chart

# 17.2.0
helm pull bitnami/postgresql --version 16.4.3 --untar --untardir ./tmp
cp -r ./tmp/postgresql/* ./postgresql/Chart
rm -rf ./tmp

# Create custom-values.yaml
touch ./postgresql/custom-values.yaml

# /home/t33n/Projects/minikube/postgresql/setup.sh
```

### 🔄 Upgrade Helm Chart
```shell
kubectl config use-context minikube
helm upgrade postgresql-dev ./postgresql/Chart --namespace dev -f ./postgresql/custom-values.yaml --atomic
```

### ❌ Delete Deployment
```shell
kubectl config use-context minikube
helm --namespace dev delete postgresql-dev
```

</details>


