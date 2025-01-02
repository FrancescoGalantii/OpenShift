# `obiettivo`
Rilasciare i certificati richiesti dal CSR tramite ROOT CA creata precedentemente

---
## prerequisiti
• Una Root CA configurata, con i seguenti file:

   • Chiave privata della Root CA: private/ca.key.pem
   
   • Certificato della Root CA: certs/ca.cert.pem
   
   • File di configurazione OpenSSL: openssl.cnf
   
• Un file CSR generato

• Una directory CA organizzata come segue:

    myCA
    ├── certs/
    ├── private/
    ├── newcerts/
    ├── crl/
    ├── index.txt (vuoto inizialmente)
    ├── serial (contenente "01")
---
## passaggi 
1. verifica CSR

       openssl req -noout -text -in mydomain.csr

2. modificare il file openssl aggiungendo questa sezione:

       [ usr_cert ]
       basicConstraints = CA:FALSE
       keyUsage = digitalSignature, keyEncipherment
       extendedKeyUsage = clientAuth, serverAuth
   2.1 per non ricevere questo errore --> `Error checking certificate extension config section usr_cert`
3. Lanciare il seguente comando:

       openssl ca -config openssl.cnf -extensions usr_cert -days 365 -notext -md sha256 -in mydomain.csr -out certs/mydomain.cert.pem
   3.1 se si avrà questo output:

       Check that the request matches the signature
       Signature ok
       The Subject's Distinguished Name is as follows
       countryName           :PRINTABLE:'IT'
       stateOrProvinceName   :ASN.1 12:'Lazio'
       localityName          :ASN.1 12:'Roma'
       organizationName      :ASN.1 12:'Internet Widgits Pty Ltd'
       commonName            :ASN.1 12:'francesco'
       emailAddress          :IA5STRING:'...@.local'
       Certificate is to be certified until Dec 17 09:14:01 2025 GMT (365 days)
       Sign the certificate? [y/n]:y


       1 out of 1 certificate requests certified, commit? [y/n]y
       Write out database with 1 new entries
       Database updated
   3.2 vorrà dire che il certificato che OpenSSL ha verificato e firmato con successo il CSR utilizzando la Root CA.
---
## spiegazione output
1. **`Check that the request matches the signature`**
   --> Questo passaggio verifica che il CSR non sia stato alterato e che corrisponda alla chiave privata utilizzata per generarlo.
   
   • Signature ok conferma che la firma del CSR è valida.
   
3. **`The Subject's Distinguished Name is as follows`**
   --> OpenSSL ti mostra i dettagli del soggetto (Subject DN) estratti dal CSR.
   
   • Questi campi rappresentano il titolare del certificato:
   
5. **`Certificate is to be certified until`**
   --> Questa riga specifica la durata del certificato, in base al valore passato con l'opzione `-days`
   
7. **`Sign the certificate? [y/n]`:y**
   --> Ti viene chiesto di confermare se vuoi firmare il certificato.
   
      • Quando digiti y, OpenSSL procede a creare il certificato e lo salva nel percorso specificato nel comando.
   
9. **`1 out of 1 certificate requests certified, commit? [y/n]`:y**
   --> OpenSSL ti chiede un’ulteriore conferma per aggiornare il database della CA, che tiene traccia dei certificati emessi.
   
      •Premendo y, OpenSSL:
       Aggiunge i dettagli del certificato al file index.txt (il database della CA) e --> Incrementa il numero di serie in serial
   
11. **`Write out database with 1 new entries`**
    --> Conferma che il certificato è stato emesso e registrato correttamente nel database


       

       
