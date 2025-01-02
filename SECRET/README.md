# `Gestione e Lettura dei Secret in Kubernetes`

Questo esercizio illustra come creare e leggere Secret in Kubernetes utilizzando un Pod. I Secret sono oggetti di Kubernetes progettati per gestire dati sensibili come password, token, o chiavi di accesso in modo sicuro.

---

## Obiettivi dell'esercizio
1. Creare diversi tipi di Secret in Kubernetes.
2. Montare i Secret nei Pod in modi differenti (come volume o variabili d'ambiente).
3. Leggere i Secret all'interno di un Pod per verificarne l'accesso.

---

## Prerequisiti
- Un cluster Kubernetes configurato.
- `kubectl` installato e configurato per connettersi al cluster.
- Accesso a una shell per eseguire i comandi.

---

## Passaggi

### 1. Creare Secret

#### 1.1. Creare un Secret generico
```bash
kubectl create secret generic my-secret \
--from-literal=username=my-user \
--from-literal=password=my-password
```

#### 1.2. Creare un Secret da file
```bash
echo -n 'my-password' > ./password.txt
kubectl create secret generic file-secret --from-file=password=./password.txt
```

#### 1.3. Creare un Secret da un file YAML
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-yaml-secret
  type: Opaque
  data:
    username: bXktdXNlcg== # Base64 encoded valore di 'my-user'
    password: bXktcGFzc3dvcmQ= # Base64 encoded valore di 'my-password'
```
Applica il file:
```bash
kubectl apply -f my-yaml-secret.yaml
```

#### 1.4. Creare un Secret Docker Registry
```bash
kubectl create secret docker-registry my-reg-secret \
--docker-username=my-username \
--docker-password=my-password \
--docker-email=my-email@example.com
```

---

### 2. Leggere Secret tramite un Pod

I Secret possono essere letti da un Pod in due modi principali: come volume o come variabili d'ambiente.

#### 2.1. Montare Secret come Volume

1. Definire un Pod che monta un Secret:
   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: secret-pod
   spec:
     containers:
     - name: my-container
       image: alpine
       command: ["/bin/sh", "-c", "sleep 3600"]
       volumeMounts:
       - name: secret-volume
         mountPath: "/etc/secret"
         readOnly: true
     volumes:
     - name: secret-volume
       secret:
         secretName: my-secret
   ```
2. Applica il file YAML:
   ```bash
   kubectl apply -f pod-with-secret.yaml
   ```

3. Accedi al Pod e verifica i file montati:
   ```bash
   kubectl exec -it secret-pod -- sh
   cat /etc/secret/username
   cat /etc/secret/password
   ```

#### 2.2. Usare Secret come Variabili d'Ambiente

1. Definire un Pod con variabili d'ambiente:
   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: env-secret-pod
   spec:
     containers:
     - name: my-container
       image: alpine
       command: ["/bin/sh", "-c", "env; sleep 3600"]
       env:
       - name: USERNAME
         valueFrom:
           secretKeyRef:
             name: my-secret
             key: username
       - name: PASSWORD
         valueFrom:
           secretKeyRef:
             name: my-secret
             key: password
   ```
2. Applica il file YAML:
   ```bash
   kubectl apply -f pod-with-env-secret.yaml
   ```

3. Controlla i log del Pod per vedere le variabili d'ambiente:
   ```bash
   kubectl logs env-secret-pod
   ```

---

### 3. Debug e Validazione

- Per verificare che un Secret sia stato creato correttamente:
  ```bash
  kubectl get secret my-secret -o yaml
  ```
- Decodifica i valori Base64 dei dati:
  ```bash
  echo 'bXktdXNlcg==' | base64 --decode
  ```

---

## Risultati attesi
- I Secret sono accessibili ai Pod nei modi configurati (volume o variabili d'ambiente).
- È possibile leggere e verificare i valori dei Secret all'interno del Pod.

---

## Note
- Utilizza i Secret con attenzione in ambienti di produzione per evitare la loro esposizione accidentale.
- Abilita il controllo degli accessi RBAC per garantire che solo gli utenti o i Pod autorizzati possano accedere ai Secret.


