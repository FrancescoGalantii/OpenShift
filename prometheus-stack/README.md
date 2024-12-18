# `obiettivo`
Installare Prometheus Stack tramite helm chart uffiale --> `https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack`
e provare il BlackBox Exporter per il monitoring degli endpoint http.
## passaggi 
1. Installare Prometheus Stack tramite helm chart uffiale

       helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
       helm repo update
       helm install [RELEASE_NAME] prometheus-community/kube-prometheus-stack
2. abilitare il BlackBox Exporter
   *Si hanno due modi differenti per poterlo fare*
   2.1 **da linea di comando**

       helm upgrade prometheus-stack prometheus-community/kube-prometheus-stack \
       --set blackboxExporter.enabled=true
   se andato a buon fine avrete questo output

       Release "prometheus-stack" has been upgraded. Happy Helming!
       NAME: prometheus-stack
       LAST DEPLOYED: Wed Dec 18 10:08:47 2024
       NAMESPACE: default
       STATUS: deployed
       REVISION: 2
       NOTES:
   Per poterlo testare bisogna esporre il servizio lanciare il comando --> `kubectl port-forward svc/prometheus-blackbox-exporter 9115:9115`

   Fatto cio si può accedere al BlackBox Exporter con --> `http://localhost:9115` oppure manualmente con `curl "http://localhost:9115/probe?target=https://example.com&module=http_2xx"`
   
   2.2 **attraverso una configMap**
   • creazione file blackbox.yml e configMap blackbox-config.yaml da applicare al namespace del prometheus stack

      vi blackbox.yml

       modules:
         http_2xx:
           prober: http
           timeout: 5s
           http:
             valid_http_versions: [ "HTTP/1.1", "HTTP/2"]
             valid_status_codes: []
             fail_if_ssl: false
             fail_if_not_ssl: false
           icmp:
             prober: icmp
             timeout: 5s
      vi blackbox-config.yaml

       apiVersion: v1
       kind: ConfigMap
       metadata:
         name: blackbox-config
         namespace: default
       data:
         blackbox.yml |
           modules:
             http_2xx:
           prober: http
           timeout: 5s
           http:
             valid_http_versions: [ "HTTP/1.1", "HTTP/2"]
             valid_status_codes: []
             fail_if_ssl: false
             fail_if_not_ssl: false
           icmp:
             prober: icmp
             timeout: 5s
      • applicazione della configurazione --> `kubectl apply -f blackbox-config.yaml` o `oc apply -f blackbox-config.yaml //se si sta usando OpenShift`

      • abilitare il BlackBox Exporter

        helm upgrade prometheus-stack prometheus-community/kube-prometheus-stack \
        --set blackboxExporter.enabled=true \
        --set blackboxExporter.configName=blackbox-config \
        -n default

      • configurare prometheus per usare il blackbox Exporter
        vi blackbox-servicemonitor.yaml

        apiVersion: monitoring.coreos.com/v1
        kind: ServiceMonitor
        metadata:
          name: blackbox-exporter
          namespace: default
        spec:
          selector:
            matchLabels:
              app.kubernetes.io/name: prometheus-blackbox-exporter
          endpoints:
            - port: http
              path: /probe
              params:
                module: [http_2xx]
              interval: 30s
              scrapeTimeout: 10s
              relabelings:
                - sourceLabels: [__address__]
                  targetLabel: __param_target
                - sourcelabels: [__param_target]
                  targetLabel: instance
                - targetLabel: __address__
                  replacement: prometheus-blackbox-exporter.monitoring.svc:9115
     •  applicare il file al cluster --> `kubectl apply -f blackbox-servicemonitor.yaml

     •  aggiungere un target da monitorare
        vi blackbox-targets.yaml

        apiVersion: v1
        kind: ConfigMap
        metadata:
          name: blackbox-targets
          namespace: default
        data:
          targets.json |
            [
              {
                "labels": {
                  "job": "blackbox"
                },
                "targets": [
                  "https://example.com"
                  "http://my-service.default.svc.cluster.local"
                ]
              }
            ]
      • applicare la configurazione --> `kubectl apply -f blackbox-targets.yaml`

           
   
       





