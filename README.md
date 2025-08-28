# postgresql-cheat-sheet












# Dump


## Restore

### pgAdmin

Create new database > right click restore > Select sql file > click restore


<br><br>
<br><br>





## Create

## Single File


### pgAdmin

1. **pgAdmin starten** (meist `pgAdmin 4`).
2. Links im Browser-Baum deine **Server-Verbindung** öffnen.

   * Host: `localhost`
   * Port: (bei Charly meist `5433` oder `5434`)
   * User: `charly` oder `postgres`
   * PW: siehe Installation / `pg_hba.conf`.
3. Rechtsklick auf die **Datenbank z.b. `charly`** → **Backup…**

---

### 🔹 Backup-Dialog einstellen
Absolut. Um eine **vollständige und wiederherstellbare** Datenbanksicherung in pgAdmin zu erstellen, müssen Sie sicherstellen, dass sowohl die Struktur (Schemata, Tabellen, etc.) als auch die Daten selbst exportiert werden.

Führen Sie die folgenden Schritte aus und achten Sie genau auf die fett markierten Optionen.

### Schritt-für-Schritt-Anleitung für einen vollständigen Export

1.  **Datenbank auswählen:** Machen Sie im Objekt-Browser auf der linken Seite einen Rechtsklick auf die Datenbank, die Sie exportieren möchten (z. B. `charly-olap2`).
2.  **Backup starten:** Wählen Sie im Kontextmenü **"Backup..."**. Es öffnet sich ein neues Fenster.

  *(Beispiel-Screenshot)*

### Wichtige Einstellungen im "Backup"-Fenster

#### Tab: "General"

*   **Filename:** Wählen Sie einen Speicherort und einen aussagekräftigen Namen für Ihre Backup-Datei (z. B. `charly-pvs-backup_2025-08-28.sql`).
*   **Format:** Hier müssen Sie eine wichtige Entscheidung treffen.
    *   **Custom:** **Dies ist die empfohlene Option.** Das Ergebnis ist eine komprimierte Binärdatei, die sehr flexibel beim Wiederherstellen ist. Sie können damit auch nur einzelne Teile der Datenbank wiederherstellen. Zum Importieren benötigen Sie das Werkzeug "Restore".
    *   **Plain:** Dies erzeugt eine `.sql`-Textdatei mit allen SQL-Befehlen. Sie ist für Menschen lesbar und universell einsetzbar, aber oft sehr groß und weniger flexibel beim Import.
    *   **Tar / Directory:** Diese Formate sind für spezielle Anwendungsfälle und seltener notwendig. Für einen vollständigen Einzel-Datei-Export ist **Custom** oder **Plain** die beste Wahl.

#### Tab: "Dump Options"

Hier sind die entscheidenden Einstellungen für die Vollständigkeit.

*   **Sections:**
    *   ✅ **Pre-data:** **MUSS** angehakt sein. Dies exportiert die Definitionen der Objekte, also die Struktur Ihrer Datenbank (Tabellen, Schemata etc.).
    *   ✅ **Data:** **MUSS** angehakt sein. Dies exportiert alle eigentlichen Daten aus den Tabellen.
    *   ✅ **Post-data:** **MUSS** angehakt sein. Dies exportiert die Befehle, die nach dem Einfügen der Daten ausgeführt werden müssen, wie z. B. das Erstellen von Indizes, Triggern und Constraints (Fremdschlüsseln).

*   **Type of objects:**
    *   Stellen Sie sicher, dass hier **keine** Haken gesetzt sind, um Objekte auszuschließen (z. B. "Only data" oder "Only schema"). Sie wollen beides.

*   **Do not save:**
    *   Stellen Sie sicher, dass hier **keine** Haken gesetzt sind, insbesondere nicht bei "Owner" oder "Privileges". Sie wollen diese Informationen in der Regel mitspeichern.

*   **Queries:**
*   *   ✅ **Include IF ELSE Statement:** Empfohlen.
    *   ✅ **Use Insert Commands:** Empfohlen.
    *   ✅ **Include CREATE DATABASE statement:** **WICHTIG!** Setzen Sie diesen Haken. Dadurch wird beim späteren Import der `CREATE DATABASE`-Befehl mit in die Datei geschrieben, was die Wiederherstellung auf einem neuen System vereinfacht.
    *   ✅ **Include DROP DATABASE statement:** Optional, aber nützlich, wenn die Zieldatenbank vor dem Import komplett sauber gelöscht werden soll. Vorsicht bei der Verwendung!

*   **Table Options:**
    *   ✅ **Use Column Inserts:** Empfohlen. Dies macht das Skript robuster gegenüber Änderungen in der Spaltenreihenfolge.

#### Zusammenfassung der wichtigsten Optionen:

| Tab | Option | Empfohlene Einstellung | Grund |
| :--- | :--- | :--- | :--- |
| **General** | Format | **Custom** | Flexibelste und sicherste Methode für den Restore. |
| **Dump Options**| Sections | ✅ **Pre-data**<br>✅ **Data**<br>✅ **Post-data** | Stellt sicher, dass Struktur, Daten und Indizes/Constraints exportiert werden. |
| **Dump Options**| Queries | ✅ **Include CREATE DATABASE statement** | Erleichtert das Wiederherstellen, da die Datenbank selbst angelegt wird. |

Wenn Sie diese Einstellungen verwenden, erhalten Sie eine Datei, die alles Notwendige enthält, um die Datenbank auf einem anderen (oder demselben) Server vollständig wiederherzustellen. Klicken Sie am Ende auf **"Backup"**, um den Vorgang zu starten.





















<br><br>

--- 

<br><br>

# Table

## Show all tables


### Query
PGadmin: Rechtsklick auf die DB → Query Tool →
```sql
SELECT table_name
FROM information_schema.tables
WHERE table_schema = 'public'
ORDER BY table_name;
```


<br><br>


### PGadmin

Prima, das ist ein wichtiger Schritt! Es gibt mehrere einfache Wege in pgAdmin, um zu überprüfen, ob die Tabellen und ihre Inhalte korrekt importiert wurden.

#### Show Schema

Das ist der direkteste Weg, um einen Überblick über die Struktur zu bekommen.

1.  **Datenbank aufklappen:** Klicken Sie auf den kleinen Pfeil neben Ihrer importierten Datenbank (z. B. `Charly`).
2.  **Schemas aufklappen:** Klappen Sie den Ordner `Schemas` auf.
3.  **Public-Schema aufklappen:** Klappen Sie das Schema `public` auf (hier liegen meistens die Tabellen).
4.  **Tabellen anzeigen:** Klicken Sie auf den Ordner `Tables`. Jetzt sehen Sie eine Liste aller importierten Tabellen.
5.  **Spalten (Columns) ansehen:** Klicken Sie auf den Pfeil neben einer Tabelle, die Sie interessiert (z. B. `patient`). Darunter erscheint ein Ordner `Columns`. Wenn Sie diesen aufklappen, sehen Sie die Namen und Datentypen aller Spalten in dieser Tabelle.

Dieser Weg zeigt Ihnen die **Struktur** (also ob die Tabellen und Spalten da sind), aber noch nicht die **Daten** selbst.


<br><br>


#### Show values

Nachdem Sie eine Tabelle im Objekt-Browser gefunden haben, ist das der einfachste Weg, um einen Blick auf die Daten zu werfen.

1.  Machen Sie einen **Rechtsklick** auf den Namen der gewünschten Tabelle (z. B. `patient`).
2.  Gehen Sie im Menü auf **"View/Edit Data"**.
3.  Wählen Sie **"First 100 Rows"**.

Es öffnet sich ein neuer Tab namens "Data Output" im Hauptfenster, der Ihnen die ersten 100 Zeilen der Tabelle in einer übersichtlichen Ansicht anzeigt. Das ist der beste Weg, um schnell zu prüfen, ob der Datenimport geklappt hat.






















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


