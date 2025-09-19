---
title: "Kubernetes Intro Lab met k3d en Windows"
weight: 5
author: Arne Duyver
draft: true
---
<!-- TODO nakijken -->
## Doel

* Studenten leren **hoe Kubernetes werkt** (master, node, pods, services, load balancing, failover).
* Praktijk: 2 Windows laptops, elk met Docker Desktop + k3d.
* Cluster met 2 nodes, één Flask webapp die bezoekers telt.
* Test: load balancing met 100 requests + node crash simulatie.

---

## Wat is Kubernetes en het verschil met k3s en k3d

### Kubernetes (K8s)

* Kubernetes (afgekort K8s) is een **container orchestrator**: software die containers (zoals Docker containers) start, beheert, schaalt en monitort.
* Kernconcepten: *nodes*, *pods*, *deployments*, *services*, *load balancing*, *self-healing*.
* Volwaardige Kubernetes clusters zijn complex en zwaar om te draaien.

### k3s

* k3s is een **lichte versie van Kubernetes** ontwikkeld door Rancher (nu SUSE).
* Voordelen:

  * Kleinere binaries, eenvoudiger installatie.
  * Minder afhankelijkheden, ideaal voor edge devices (zoals Raspberry Pi).
  * Volledig compatibel met Kubernetes API (alles wat je in K8s kan, kan je in k3s).

### k3d

* k3d is een **wrapper rond k3s** die k3s runt in Docker containers.
* Doel: heel snel een k3s-cluster opzetten op een lokale laptop/desktop.
* Voordelen:

  * Supersnel opstarten en opruimen.
  * Werkt goed op Windows + Docker Desktop.
  * Perfect voor demo’s, testen en lokaal oefenen.

➡️ Kort samengevat:

* **Kubernetes (K8s)** = het standaard, zware platform.
* **k3s** = lichtgewicht Kubernetes, geschikt voor IoT/edge.
* **k3d** = k3s in Docker, ideaal voor lokaal oefenen en demo’s.

---

## Voorbereiding (docent)

* Zorg dat beide laptops:

  * **Windows 10/11** draaien.
  * **Docker Desktop** geïnstalleerd en gestart hebben.
  * **k3d** geïnstalleerd (zie instructies hieronder).
  * In hetzelfde WiFi netwerk zitten (één hotspot volstaat, zolang laptops elkaar kunnen pingen).
  * **kubectl** wordt samen met ... geïnstalleerd???  <!--TODO-->

<!-- TODO: New-NetFirewallRule -DisplayName "K3s Cluster" -Direction Inbound -Protocol TCP -LocalPort 6443,10250-10255,30080 -Action Allow -->

<details>
<summary>Installatie Docker Desktop</summary>

1. Download: [https://www.docker.com/products/docker-desktop/](https://www.docker.com/products/docker-desktop/)
2. Installeren met default settings.
3. Start Docker Desktop en check:

   ```powershell
   docker version
   ```

   Als dit werkt -> ok.

</details>

<details>
<summary>Installatie k3d</summary>

1. Download binary: [https://k3d.io/v5.6.0/#installation](https://k3d.io/v5.6.0/#installation) (choco install k3d) OF [k3d.exe via github](https://github.com/k3d-io/k3d/releases)
2. Voeg `k3d.exe` toe aan PATH.
3. Test:

   ```powershell
   k3d version
   ```

   -> zou een versienummer moeten tonen.

</details>

---

## Stap 1 — Cluster aanmaken op Laptop A (master)

Laptop A creëert een k3s cluster dat externe nodes kan accepteren.

```powershell
# Vervang <LAPTOP_A_IP> door het werkelijke IP adres van Laptop A (ipconfig)
k3d cluster create demo-cluster --servers 1 --agents 0 -p "8080:30080@loadbalancer" --api-port <LAPTOP_A_IP>:6443
```
<!-- k3d cluster create demo-cluster --servers 1 --agents 1 -p "8080:80@loadbalancer" --api-port 10.209.145.166:6443 -->

Om op te ruimen: `k3d cluster delete demo-cluster`
Na restart connection refused doe dan even: `k3d cluster stop demo-cluster; k3d cluster start demo-cluster`

* `--servers 1`: de control plane (master).
* `--agents 1`: één worker node op Laptop A.
* `-p "8080:80@loadbalancer"`: mapt poort 8080 op Windows naar poort 80 in cluster.
* `--api-port <LAPTOP_A_IP>:6443`: API server toegankelijk vanaf andere laptops.

Check:

```powershell
kubectl get nodes
```

Example output:
```powershell
PS C:\git\cloud-demos-exercises-docent\k3d> kubectl get nodes
NAME                        STATUS   ROLES                  AGE   VERSION
k3d-demo-cluster-agent-0    Ready    <none>                 10m   v1.31.5+k3s1
k3d-demo-cluster-server-0   Ready    control-plane,master   10m   v1.31.5+k3s1
```

---

## Stap 2 — Node (agent) toevoegen op Laptop B

Laptop B join’t het cluster van Laptop A.

<!-- TODO: niet meet nodig
1. **Op Laptop A**: Exporteer de clusterconfig:

   ```powershell
   k3d kubeconfig get demo-cluster > kubeconfig.yaml
   ``` -->

1. **Op Laptop A**: Haal de node token op:

   ```powershell
   docker exec k3d-demo-cluster-server-0 cat /var/lib/rancher/k3s/server/node-token
   ```
   - `docker exec` - Runs a command inside a Docker container
   - `k3d-demo-cluster-server-0` - The name of the k3d server container
   - `cat /var/lib/rancher/k3s/server/node-token` - Reads the actual join token file that k3s creates

2. Deel `kubeconfig.yaml` met Laptop B (via USB of file share).

3. **Op Laptop B**: Join het cluster als externe node (vervang <LAPTOP_A_IP> en <TOKEN>):

     ```powershell
     docker run -d --name k3s-agent --restart=unless-stopped --privileged -p 30080:30080 -p 10250:10250 rancher/k3s:latest agent --server https://<LAPTOP_A_IP>:6443 --token <TOKEN>
     ```
     <!-- docker run -d --name k3s-agent --restart=unless-stopped --privileged rancher/k3s:latest agent --server https://10.209.145.166:6443 --token K108428e10ef66107779bbba82f045d33b7000db988d83bba28fe43e9f2e2de5a72::server:tzBcpyicksiZPvbylXqi -->

Check op Laptop A:

```powershell
kubectl get nodes
```
-> zou nu 2 nodes moeten tonen (1 op Laptop A, 1 op Laptop B):

Example output:
```powerhell
PS C:\git\cloud-demos-exercises-docent\k3d> kubectl get nodes
NAME                        STATUS     ROLES                  AGE   VERSION
a9b9908eabba                NotReady   <none>                 1s    v1.31.12+k3s1
k3d-demo-cluster-agent-0    Ready      <none>                 10m   v1.31.5+k3s1
k3d-demo-cluster-server-0   Ready      control-plane,master   10m   v1.31.5+k3s1
```

---
<!-- TODO: niet meer nodig want halen image van dockerhub
## Stap 3 — Flask webapp deployen

1. **Maak de Flask app met connectie tracking**:

<details>
<summary>app.py</summary>

```python
# app.py
import os
import uuid
from flask import Flask, render_template_string, request
from flask_socketio import SocketIO, emit

# Identify this server/pod (helps when running multiple pods)
SERVER_ID = os.environ.get("POD_NAME") or os.environ.get("HOSTNAME") or str(uuid.uuid4())[:8]

app = Flask(__name__)
# allow CORS from anywhere for easy testing in cluster
socketio = SocketIO(app, cors_allowed_origins="*")

# track connected client session ids
clients = set()

INDEX_HTML = """
<!doctype html>
<html>
<head>
  <meta charset="utf-8" />
  <title>Connections on {{ server_id }}</title>
  <style>
    body { font-family: system-ui, -apple-system, "Segoe UI", Roboto, Arial; padding: 2rem; }
    .card { padding: 1.2rem; border-radius: 8px; box-shadow: 0 6px 18px rgba(0,0,0,0.08); max-width: 460px; }
    .big { font-size: 64px; font-weight: 700; margin: .2rem 0; }
    .muted { color: #666; }
  </style>
</head>
<body>
  <div class="card">
    <div class="muted">Server / Pod:</div>
    <div><strong id="server">{{ server_id }}</strong></div>
    <hr/>
    <div class="muted">Connected tabs (this pod)</div>
    <div class="big" id="count">—</div>
    <div class="muted" id="sid">session: —</div>
    <p class="muted">Open more tabs (same browser or other browsers) to increase the count. Each tab maintains its own websocket connection.</p>
  </div>

  <script src="https://cdn.socket.io/4.7.5/socket.io.min.js"></script>
  <script>
    const socket = io({ transports: ["websocket"] });

    socket.on("connect", () => {
      document.getElementById("sid").textContent = "session: " + socket.id;
    });

    socket.on("count", (data) => {
      document.getElementById("count").textContent = data.count;
      document.getElementById("server").textContent = data.server || "{{ server_id }}";
    });

    socket.on("disconnect", () => {
      document.getElementById("count").textContent = "disconnected";
    });
  </script>
</body>
</html>
"""

@app.route("/")
def index():
    return render_template_string(INDEX_HTML, server_id=SERVER_ID)

@socketio.on("connect")
def handle_connect():
    sid = request.sid
    clients.add(sid)
    emit("count", {"count": len(clients), "server": SERVER_ID}, broadcast=True)

@socketio.on("disconnect")
def handle_disconnect():
    sid = request.sid
    clients.discard(sid)
    emit("count", {"count": len(clients), "server": SERVER_ID}, broadcast=True)

if __name__ == "__main__":
    import eventlet
    import eventlet.wsgi
    socketio.run(app, host="0.0.0.0", port=5000)
```

</details>

2. **Maak requirements.txt**:

<details>
<summary>requirements.txt</summary>

```
flask
flask-socketio
eventlet
```

</details>

3. **Maak een Dockerfile**:

<details>
<summary>Dockerfile</summary>

```dockerfile
FROM python:3.11-slim

# Stel werkdirectory in
WORKDIR /app

# Kopieer requirements en installeer dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Kopieer app code
COPY app.py .

# Expose poort 5000
EXPOSE 5000

# Start de Flask app
CMD ["python", "app.py"]
```

</details>


4. **Bouw de Flask image lokaal op Laptop A**:
   ```powershell
   docker build -t flask-counter:latest .
   ```

5. **Load de image in k3d cluster**:
   ```powershell
   k3d image import flask-counter:latest -c demo-cluster
   ```
   
   Dit zorgt ervoor dat alle nodes in het cluster (inclusief Laptop B) toegang hebben tot de image. 
-->

## Stap 3 —  Maak `flask-app.yaml`en deploy:

<details>
<summary>flask-app.yaml</summary>

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: flask-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: flask
  template:
    metadata:
      labels:
        app: flask
    spec:
      containers:
      - name: flask
        image: arneduyver/loadbalancetester-flask:latest
        ports:
        - containerPort: 5000
---
apiVersion: v1
kind: Service
metadata:
  name: flask-service
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 5000
  selector:
    app: flask
```

</details>

Deploy:

```powershell
kubectl apply -f flask-app.yaml
kubectl get pods -o wide
```
Om op te ruimen: `kubectl delete -f flask-app.yaml`

### Uitleg flask-app.yaml configuratie

Het YAML bestand bevat twee Kubernetes objecten:

#### Deployment
- **`replicas: 2`** - Start 2 identieke pods van de Flask app
- **`selector.matchLabels`** - Koppelt de Deployment aan pods met label `app: flask`
- **`template.metadata.labels`** - Geeft elk pod het label `app: flask`
- **`image: flask-counter:latest`** - Gebruikt onze lokaal gebouwde image
- **`imagePullPolicy: Never`** - Belangrijk: gebruik alleen lokale image, pull niet van internet
- **`containerPort: 5000`** - Flask app draait op poort 5000 binnen de container

#### Service
- **`type: LoadBalancer`** - Verdeelt verkeer over alle beschikbare pods
- **`port: 80`** - De Service is bereikbaar op poort 80
- **`targetPort: 5000`** - Stuurt verkeer door naar poort 5000 van de pods
- **`selector: app: flask`** - Zoekt alle pods met label `app: flask`

=> **Resultaat**: Kubernetes start 2 Flask pods en verdeelt automatisch verkeer tussen beide.


<!-- TODO? : kubectl port-forward pod/flask-app-5ffbc584f7-g84b8 8085:5000 
svclb = “service load balancer” used by k3d to emulate a cloud LoadBalancer for Kubernetes Services of type LoadBalancer. svclb is an emulator — not a full cloud LB. It only proxies where it can reach the node/container endpoints. That’s why it didn’t forward to your laptop B unless laptop B exposed NodePort on a reachable host interface.-->
---

## Stap 4 — Test load balancing met persistente connecties

1. **Open in browser**: `http://localhost:8080` -> je ziet de Flask app met 1 actieve connectie (jouw browser)

2. **Start 20 persistente connecties** om load balancing te demonstreren:

Open 20 browser tabs op laptop A met een verbinding naar `http://localhost:8080`.

3. **Monitor de verdeling**:
   - Refresh de browser op `http://localhost:8080`
   - Je ziet afwisselend de verschillende pods (met verschillende hostnames)
   - Elke pod toont ~10 actieve connecties
   - -> **Dit toont load balancing in actie!**

---

## Stap 5 — Node crash simuleren <!-- TODO: kubectl delete pod <pod-name> -->

1. **Terwijl de 20 connecties nog actief zijn**, crash Laptop B:

   **Op Laptop B**:
   ```powershell
   
   docker stop k3s-agent
   docker rm k3s-agent
   ```

2. **Op Laptop A**: Kijk hoe de pods herschedulen:

   ```powershell
   kubectl get pods -o wide --watch
   ```

3. **Monitor in browser**: Refresh `http://localhost:8080`
   - Je ziet nu **alle ~20 connecties** op één pod (van Laptop A)
   - Kubernetes heeft automatisch de connecties overgenomen!
   - -> **Dit toont failover in actie!**

---

## Nabespreking

* Kubernetes verdeelt verkeer automatisch over pods/nodes.
* Als een node crasht, neemt de andere het over.
* LoadBalancer + Service abstraheren de complexiteit.

---

## Docker Swarm vs Kubernetes (Extraatje)

### Wat is Docker Swarm?

Docker Swarm is **Docker's eigen container orchestrator** - een concurrent van Kubernetes. Net zoals Kubernetes kan het:
* Containers over meerdere machines beheren
* Load balancing tussen containers
* Automatic failover bij node crashes
* Service discovery en networking

### Belangrijkste verschillen

| Aspect | **Docker Swarm** | **Kubernetes** |
|--------|------------------|----------------|
| **Complexiteit** | Eenvoudig, weinig configuratie | Complex, veel configuratie opties |
| **Leercurve** | Vlak - als je Docker kent, ken je Swarm | Steile leercurve, veel concepten |
| **Architectuur** | Manager + Worker nodes | Master + Worker nodes + etcd |
| **Configuratie** | Docker Compose YAML | Kubernetes YAML (complexer) |
| **Netwerking** | Overlay networks (simpel) | CNI plugins (flexibel maar complex) |
| **Schaalbaarheid** | Tot ~1000 nodes | Tot 5000+ nodes |
| **Ecosystem** | Beperkt, vooral Docker tools | Enorm ecosystem (Helm, Operators, etc.) |
| **Enterprise support** | Docker Enterprise | Kubernetes overal (AWS EKS, Azure AKS, etc.) |

### Wanneer Docker Swarm?

**Voordelen van Swarm:**
* **Snelle setup** - `docker swarm init` en klaar
* **Familiar** - zelfde commando's als Docker
* **Minder overhead** - geen extra tools nodig
* **Goed voor kleinere setups** - 2-10 nodes

**Voorbeeld Swarm setup:**
```bash
# Op manager node
docker swarm init
docker service create --replicas 3 --publish 80:5000 flask-app

# Op worker nodes  
docker swarm join --token <TOKEN> <MANAGER-IP>:2377
```

### Wanneer Kubernetes?

**Voordelen van Kubernetes:**
* **Industry standard** - wordt overal gebruikt
* **Zeer flexibel** - oneindig aanpasbaar
* **Grote community** - veel tools en ondersteuning
* **Cloud native** - alle cloud providers ondersteunen het
* **Toekomstbestendig** - actieve ontwikkeling

### Conclusie

* **Docker Swarm** = eenvoud, snelle start, kleinere projecten
* **Kubernetes** = kracht, flexibiliteit, enterprise-ready

Voor dit lab kozen we Kubernetes omdat het de **industrie-standaard** is en studenten ermee zullen werken in hun carrière. Maar voor eenvoudige projecten kan Docker Swarm een uitstekende keuze zijn!

---
