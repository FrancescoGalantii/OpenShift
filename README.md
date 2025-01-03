# `OpenShift`

Questa repository contiene una serie di esercizi e progetti sviluppati durante il percorso di formazione presso la DevOps Academy, focalizzati sull'utilizzo di OpenShift per la gestione e l'orchestrazione di applicazioni containerizzate.

---
## Introduzione

Benvenuti nella repository `OpenShift`, creata come parte del percorso formativo nella DevOps Academy. Questo spazio raccoglie esercizi pratici e progetti che esplorano l'utilizzo di OpenShift, una piattaforma Kubernetes-based per l'orchestrazione di container, con l'obiettivo di approfondire le competenze essenziali nel campo del DevOps e della gestione di applicazioni cloud-native.

---
## Contenuto della Repository

All'interno di questa repository troverai:

1. **Esercizi OpenShift**: Esempi e esercizi relativi all'utilizzo di OpenShift per il deployment e la gestione di applicazioni.
2. **Script di Automazione**: Script che illustrano l'automazione di processi comuni in OpenShift.
3. **Progetti Completi**: Progetti che integrano diverse funzionalità di OpenShift per la gestione di applicazioni containerizzate.

## Struttura della Repository

La struttura della repository è organizzata come segue:
```
OpenShift
├── CSR
│   └── README.md
├── LABELS
│   ├── DaemonSet.yaml
│   ├── README.md
│   ├── deployment.yaml
│   ├── ingress.yaml
│   ├── replicaSet.yaml
│   └── service.yaml
├── NetworkPolicy
│   ├── README.md
│   ├── backend-deployment.yaml
│   ├── backend-networkpolicy.yaml
│   ├── database-deployment.yaml
│   ├── database-networkpolicy.yaml
│   ├── frontend-deployment.yaml
│   ├── frontend-networkpolicy.yaml
│   ├── service-backend.yaml
│   ├── service-database.yaml
│   └── service-frontend.yaml
├── ROOT_CA
│   └── README.md
├── ResourceQuotas
│   └── README.md
├── SECRET
│   ├── README.md
│   ├── secreat-reader-pod.yaml
│   └── secret-reader-pod-env.yaml
├── deployment
│   └── deploy.yaml
├── prometheus-stack
│   └── README.md
└── rilascio-cert
    └── README.md

10 directories, 25 files
```
---
## Requisiti

Per eseguire gli script e gli esercizi presenti in questa repository, assicurati di avere accesso a un ambiente OpenShift funzionante e di avere installato:

- [oc CLI](https://docs.openshift.com/container-platform/latest/cli_reference/openshift_cli/getting-started-cli.html) --> nel caso usiate OpenShift locale
- [kubectl CLI](https://kubernetes.io/docs/reference/kubectl/) --> nel Caso usiate un cluster Kubernetes locale(k3s, minikube etc..)
