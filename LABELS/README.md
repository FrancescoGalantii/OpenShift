# `Esercizio Kubernetes Deployment`

Questo repository contiene i file di configurazione YAML e le istruzioni per distribuire e gestire un ambiente Kubernetes composto da varie applicazioni, servizi e risorse. L'obiettivo di questo esercizio è organizzare e gestire i deployment in base all'ambiente, al tipo di applicazione e alla regione geografica.

## Obiettivi

1. Creare un Deployment per un'applicazione web nell'ambiente di sviluppo situato nella regione europea.
2. Esporre l'applicazione web utilizzando un Service di tipo LoadBalancer.
3. Creare un ReplicaSet per gestire le repliche dell'applicazione web.
4. Configurare un Ingress per esporre l'applicazione web tramite un dominio personalizzato.
5. Distribuire un DaemonSet per eseguire un'applicazione di logging su tutti i nodi del cluster.
6. Verificare la creazione e il funzionamento di tutte le risorse.

---

## Passaggi

### 1. Deployment
Il Deployment `web-app-dev` è responsabile della gestione delle repliche dei pod dell'applicazione web. È configurato con le seguenti etichette:
- `environment=dev`
- `app=web`
- `region=EU`

#### File: `deployment.yaml`
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app-dev
  labels:
    environment: dev
    app: web
    region: EU
spec:
  replicas: 3
  selector:
    matchLabels:
      environment: dev
      app: web
  template:
    metadata:
      labels:
        environment: dev
        app: web
    spec:
      containers:
        - name: web-app
          image: nginx:latest
          ports:
            - containerPort: 80
```

### 2. Service (LoadBalancer)
Il Service `web-app-service` di tipo LoadBalancer espone l'applicazione web alla rete esterna. Seleziona tutti i pod etichettati con `app=web` e `environment=dev`.

#### File: `service.yaml`
```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-app-service
  labels:
    app: web
    environment: dev
spec:
  type: LoadBalancer
  selector:
    app: web
    environment: dev
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

### 3. ReplicaSet
Il ReplicaSet `web-app-replicaset` garantisce il numero desiderato di repliche per i pod dell'applicazione web.

#### File: `replicaset.yaml`
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: web-app-replicaset
  labels:
    environment: dev
    app: web
spec:
  replicas: 3
  selector:
    matchLabels:
      environment: dev
      app: web
  template:
    metadata:
      labels:
        environment: dev
        app: web
    spec:
      containers:
        - name: web-app
          image: nginx:latest
          ports:
            - containerPort: 80
```

### 4. Ingress
L'Ingress `web-app-ingress` configura un dominio personalizzato (`web-app.dev.example.com`) per instradare il traffico verso il Service `web-app-service`.

#### File: `ingress.yaml`
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: web-app-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: web-app.dev.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-app-service
            port:
              number: 80
```

### 5. DaemonSet
Il DaemonSet `logging-daemonset` esegue un'applicazione di logging (ad esempio Fluentd) su tutti i nodi del cluster.

#### File: `daemonset.yaml`
```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: logging-daemonset
  labels:
    app: logging
spec:
  selector:
    matchLabels:
      app: logging
  template:
    metadata:
      labels:
        app: logging
    spec:
      containers:
      - name: logging-agent
        image: fluentd:latest
        resources:
          limits:
            memory: "200Mi"
            cpu: "100m"
        volumeMounts:
          - name: varlog
          mountPath: /var/log
      volumes:
        - name: varlog
          hostPath:
          path: /var/log
```

---

## Istruzioni per il Deployment

1. Salva le configurazioni YAML nei rispettivi file (ad esempio, `deployment.yaml`, `service.yaml`, ecc.).
2. Applica le configurazioni utilizzando `kubectl apply`:
   ```bash
   kubectl apply -f deployment.yaml
   kubectl apply -f service.yaml
   kubectl apply -f replicaset.yaml
   kubectl apply -f ingress.yaml
   kubectl apply -f daemonset.yaml
   ```
3. Verifica la creazione delle risorse:
   ```bash
   kubectl get deployments,services,replicasets,ingress,daemonsets -o wide
   ```
4. Controlla lo stato dei pod dell'applicazione web:
   ```bash
   kubectl get pods -l app=web,environment=dev
   ```
5. Testa l'applicazione accedendo al dominio configurato nell'Ingress (ad esempio, `http://web-app.dev.example.com`).

---

## Note

- Assicurati che il tuo cluster abbia un Ingress Controller (ad esempio NGINX) installato e configurato.
- Aggiorna il tuo DNS o il file `/etc/hosts` per puntare `web-app.dev.example.com` all'IP esterno del controller Ingress.
- Utilizza `kubectl logs` per risolvere eventuali problemi con i pod.

---

## Licenza
Questo esercizio è fornito per scopi educativi. Sentiti libero di usarlo e modificarlo secondo le necessità.
