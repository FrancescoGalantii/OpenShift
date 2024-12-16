# **`obiettivo`**
Creare ROOT CA self-signed e locale
## prerequisiti
• avere OpenSSL installato 
## passaggi per la creazione
1. creazione directory per la CA
    
       mkdir -p ~/myCA
       cd ~/myCA
       mkdir certs crl newcerts private
       chmod 700 private
       touch index.txt
       echo 1000 > serial
2. generazione chiave privata per la ROOT CA

        openssl genrsa -aes256 -out private/ca.key.pem 4096
        chmod 400 private/ca.key.pem
3. creazione di un certificato self-signed per la ROOT CA

        openssl req -x509 -new -nodes -key private/ca.key.pem -sha256 -days 3650 -out certs/ca.cert.pem
*!!Durante la creazione verrà chiesto di inserire delle informazioni:*

  •   Country Name (2 letter code) [AU]:
  
  •   State or Province Name (full name) [Some-State]:
  
  •   Locality Name (eg, city) []:
  
  •   Organization Name (eg, company) [Internet Widgits Pty Ltd]:
  
  •   Organizational Unit Name (eg, section) []:
  
  •   Common Name (e.g. server FQDN or YOUR name) []:
  
  •   Email Address []:
## verifica generazione certificato 

    openssl x509 -noout -text -in certs/ca.cert.pem
## consigli aggiuntivi 
• Conservare in sicurezza la chiave privata della ROOT CA

• impostare una durata sufficiente per evitare di rigenerare frequentemente i certificati --> `nel mio caso ho impostato una durata di 3650 giorni grazie all'opzione -days 3650` 

• eseguire la CA in un ambiente se possibile isolato per garantire sicurezza.

