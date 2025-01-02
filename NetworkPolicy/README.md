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

• Applicazione frontend: Espone un servizio web all'esterno del cluster.
• Applicazione backend: Fornisce servizi all'applicazione frontend.
• Database: Contiene i dati delle applicazioni.


