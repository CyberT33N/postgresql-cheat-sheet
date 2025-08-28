# postgresql-cheat-sheet



# Dump


## pgAdmin

1. **pgAdmin starten** (meist `pgAdmin 4`).
2. Links im Browser-Baum deine **Server-Verbindung** öffnen.

   * Host: `localhost`
   * Port: (bei Charly meist `5433` oder `5434`)
   * User: `charly` oder `postgres`
   * PW: siehe Installation / `pg_hba.conf`.
3. Rechtsklick auf die **Datenbank z.b. `charly`** → **Backup…**

---

### 🔹 Backup-Dialog einstellen

* **Format**:

  * `Custom` (empfohlen, flexibler für Restore mit `pg_restore`)
  * oder `Plain` (SQL-Datei, kann man direkt durchlaufen lassen).
* **Filename**:

  * z. B. `C:\Backup\charly.backup`
* **Dump Options #1**:

  * `Include CREATE DATABASE` aktivieren, wenn du eine 1:1-Kopie willst.
* **Dump Options #2**:

  * Standard reicht. Bei Bedarf `Blobs` anhaken.
* **Restore-Kompatibilität**:

  * Wenn du später in der gleichen Charly-Installation wiederherstellst, kannst du alles so lassen.

---

### 🔹 Dump starten

* Mit **OK** ausführen → unten in pgAdmin erscheint der Fortschritt.
* Datei landet da, wo du’s angegeben hast.

---

### 🔹 Restore (nur falls nötig)

* Neue/leere DB erstellen (z. B. `charly_restore`).
* Rechtsklick → **Restore…**
* Format: gleich wie beim Dump (`Custom` oder `Plain`).
* Datei auswählen → GO.

---

👉 Vorteil in pgAdmin: keine Kommandos tippen, alles klicki-bunti.
👉 Nachteil: unter der Haube ruft pgAdmin auch nur `pg_dump` / `pg_restore` auf, d. h. du brauchst Schreibrechte im Backup-Verzeichnis.























<br><br>

--- 

<br><br>










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


