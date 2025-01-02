# `obiettivo`
creare un manifest che descriva il ResourceQuotas ed applicarle verificando l'effettivo funzionamento al superamento delle soglie indicate.

---
## passaggi
1. creare il project(namespace) di prova

       oc new-project ns-quotas --display-name 'ProjectQuota' --> per OpenShift
       kubectl create ns ns-quotas --> per kubernetes
2. creare il manifest che descrive le ResourceQuotas
```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: limit-whit-quota
  namespace: ns-quotas
spec:
  hard:
    requests.cpu: "4"          
    requests.memory: "8Gi"     
    limits.cpu: "8"            
    limits.memory: "16Gi"      
    persistentvolumeclaims: "5"
    pods: "10"                   
    services: "5"              
    configmaps: "10"           
    secrets: "10"             
    requests.storage: "100Gi" // storage.requests per OpenShift 
```
3. applicare le ResourceQuotas

       kubectl apply -f resource-quotas.yaml --> per kubernetes
       oc apply -f resource-quotas.yaml --> per OpenShift
---
## testare il funzionamento 
Affinche si possa vedere il funzionamento delle ResourceQuotas si deve creare qualcosa che superi quel limite di risorse impostate nel mio caso un pod.
1. creazione pod
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-over-quota
  namespace: ns-quotas
    spec:
    containers:
      - name: over-quota-container
        image: busybox
        command: ["sh", "-c", "sleep 3600"]
        resources:
        requests:
          cpu: "5"      
          memory: "2Gi" 
        limits:
          cpu: "6"      
          memory: "4Gi"
```
   1.1 si può notare che nel manifest delle ResourceQuotas request cpu è impostato con 4 quindi per il pod la impostiamo a 5
   
2. applicare il file yaml

       kubectl apply -f test-overlimit.yaml
       oc apply -f test-overlimit.yaml
   2.1 arrivati a questo punto se le resourceQuotas sono state appliacate con successo si avrà questo output

          Error from server (Forbidden): error when creating "test-overlimit.yaml": pods "test-over-quota" is forbidden: exceeded quota: limit-whit-quota, requested: requests.cpu=5, used: requests.cpu=0, limited: requests.cpu=4
   
 
