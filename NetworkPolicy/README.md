# `obiettivo`
Lo scopo di questo esercizio si incentra sulla creazione di tre applicazioni:
- Applicazione frontend: Espone un servizio web all'esterno del cluster.
- Applicazione backend: Fornisce servizi all'applicazione frontend.
- Database: Contiene i dati delle applicazioni.

e la configurazione delle Network Policy per garantire la seguente sicurezza:
- L'applicazione frontend può comunicare solo con l'applicazione backend.
- L'applicazione backend può comunicare solo con il database.
- Il database non può comunicare con l'esterno.

---

## prerequisiti

1. un cluster locale configurato correttamente
2. docker installato
3. il resto dipende se vogliate usare un applicazione già sviluppata in precedenza o magari utilizzare un server web come NGINX
---
## passaggi
#### frontend
1. creazione di un deployment per il frontend
2. creazione di una NetworkPolicy per il frontend
3. creazione di un service per il frontend
#### backend
1. creazione di un deployment per il backend
2. creazione di una NetworkPolicy per il backend
3. creazione di un service per il backend
#### database
1. creazione di un deployment per la definizione del database
2. creazione di una NetworkPolicy per il database
3. creazione service per consentire al backend di accedervi
#### applicazione manifest
```bash
kubectl apply -f frontend-deployment.yaml
kubectl apply -f frontend-networkpolicy.yaml
kubectl apply -f service-frontend.yaml
kubectl apply -f backend-deployment.yaml
kubectl apply -f backend-networkpolicy.yaml
kubectl apply -f service-backend.yaml
kubectl apply -f database-deployment.yaml
kubectl apply -f database-networkpolicy.yaml
kubectl apply -f service-database.yaml
```
---
## verifiche
#### NetworkPolicy
kubectl get networkpolicies

    NAME              POD-SELECTOR   AGE
    frontend-policy   app=frontend   20s
    backend-policy    app=backend    20s
    database-policy   app=database   20s
#### Pods
kubectl get pods dove l'output dovrà essere il seguente

    NAME                           READY    STATUS      RESTARTS   AGE 
    frontend-7dc9f949c7-6dk94       1/1     Running     0          17h
    frontend-7dc9f949c7-nzmkr       1/1     Running     0          17h
    backend-6d476f755-4l9cf         1/1     Running     0          17h      
    backend-6d476f755-ltvvd         1/1     Running     0          17h
    database-5965f678c9-gzrkn       1/1     Running     0          17h
#### comunicazione
frontend --> backend
```bash
kubectl exec -it <frontend-pod> -- curl http://backend-service:5000
```
backend --> Database
```bash
kubectl exec -it <backend-pod> -- psql -h database-service -U user -d mydb
```
Database(deve fallire)
```bash
kubectl exec -it <database-pod> -- ping google.com
```





