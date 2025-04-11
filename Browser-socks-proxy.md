---

## Utilizzo di un proxy SOCKS tramite SSH per l'intero browser

È possibile configurare un browser (ad esempio Firefox) per utilizzare un proxy SOCKS locale, inoltrato tramite un tunnel SSH. Questo approccio permette di incanalare tutto il traffico del browser attraverso un server remoto, superando eventuali restrizioni locali senza utilizzare una VPN.

### 1. Avvio del tunnel SOCKS tramite SSH

Eseguire il seguente comando da terminale:

```bash
ssh -D 1080 user@ssh.example.com -N
```

**Spiegazione dei parametri:**

- `-D 1080`: apre una porta locale (in questo caso la 1080) per creare un proxy SOCKS.
- `-N`: non esegue comandi remoti, limita la sessione al forwarding.

> È possibile utilizzare qualsiasi porta libera al posto della 1080.

### 2. Configurazione del browser (esempio con Firefox)

Firefox consente la configurazione manuale di un proxy SOCKS senza estensioni aggiuntive.

#### Passaggi:

1. Aprire Firefox e accedere a:  
   `Impostazioni > Generale > Impostazioni di rete > Impostazioni...`

2. Selezionare:  
   **"Configurazione manuale del proxy"**

3. Inserire i seguenti valori:

   - **SOCKS Host**: `127.0.0.1`
   - **Porta**: `1080`
   - Selezionare **SOCKS v5**
   - Abilitare l'opzione **"Proxy DNS quando si utilizza SOCKS v5"**

4. Salvare le modifiche e chiudere la finestra.

A questo punto, tutto il traffico del browser verrà inoltrato tramite il proxy SOCKS e uscirà dal server `ssh.example.com`.

### 3. Verifica del funzionamento

È possibile accedere a un servizio di verifica IP pubblico come:

```
https://whatismyipaddress.com
```

Oppure:

```
https://ipinfo.io
```

Se tutto è configurato correttamente, l’indirizzo IP mostrato sarà quello del server SSH remoto.

