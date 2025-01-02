# `obiettivo` 
Creare un CSR, Quando crei un file CSR, stai essenzialmente richiedendo a una CA attendibile di emettere un certificato digitale che verifichi l'identità del tuo sito web o della tua organizzazione. IL CSR è firmato con la chiave privata corrispondente, il che dimostra che possiedi la chiave privata e garantisce l'integrità del file CSR dati.

---
## passaggi creazione CSR
1. generazione chiave privata

       openssl genrsa -out mydomain.key 2048
   Questo comando genera il file mydomain.key, che contiene la chiave privata.
2. creare una CSR
   
       openssl req -new -key mydomain.key -out mydomain.csr
   Anche in questo caso verrà chiesto di inserire delle informazioni:

          Country Name (2 letter code) [AU]:
          State or Province Name (full name) [Some-State]:
          Locality Name (eg, city) []:
          Organization Name (eg, company) [Internet Widgits Pty Ltd]:
          Organizational Unit Name (eg, section) []:IT
          Common Name (e.g. server FQDN or YOUR name) []:
          Email Address []:
          A challenge password []:
          An optional company name []:`
---
   
## verifica creazione CSR

    openssl req -noout -text -in mydomain.csr
---
## Dettagli
• `mydomain.key`: La chiave privata, da conservare in sicurezza. 

• `mydomain.csr`: La richiesta CSR, da inviare a una CA (Certificate Authority) o da firmare con una CA locale.


