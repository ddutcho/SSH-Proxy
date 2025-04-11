# Bypass firewall con tunnel SSH e proxy HTTPS locale

Questa guida descrive una procedura per accedere a un servizio web remoto bloccato da un firewall locale, utilizzando un tunnel SSH e una configurazione minima lato client. Il metodo è particolarmente utile in contesti in cui non è possibile utilizzare una VPN, ma si ha accesso a un server intermedio tramite SSH.

## Indice

- [Scenario](#scenario)
- [Obiettivo](#obiettivo)
- [Procedura](#procedura)
  - [1. Modifica del file hosts](#1-modifica-del-file-hosts)
  - [2. Creazione del tunnel SSH](#2-creazione-del-tunnel-ssh)
  - [3. Accesso al servizio](#3-accesso-al-servizio)
- [Verifica della connessione](#verifica-della-connessione)
- [Esempio di script per Windows](#esempio-di-script-per-windows)
- [Utilizzo su Linux e macOS](#utilizzo-su-linux-e-macos)
- [Note finali](#note-finali)

## Scenario

- La connessione locale è limitata da un firewall che blocca l'accesso diretto a un dominio specifico (es. `domain.example`).
- È disponibile un accesso SSH a un server intermedio (es. `ssh.example.com`) con connettività libera verso l'esterno.
- Il servizio remoto è accessibile tramite HTTPS all'indirizzo `https://domain.example`.
- Non è possibile o non si desidera utilizzare una VPN.

## Obiettivo

Accedere al servizio remoto `https://domain.example` tramite browser locale, facendo transitare il traffico attraverso un tunnel SSH, senza modificare la configurazione del server remoto e mantenendo accesso ai servizi locali.

## Procedura

### 1. Modifica del file hosts

Aprire il file `hosts` con privilegi amministrativi.

Percorso su Windows:

```
C:\Windows\System32\drivers\etc\hosts
```

Aggiungere la seguente riga:

```
127.0.0.1 domain.example
```

Questa modifica forza il sistema operativo a risolvere il dominio `domain.example` come `localhost`.

### 2. Creazione del tunnel SSH

Aprire un terminale e avviare il seguente comando:

```bash
ssh -L 443:domain.example:443 user@ssh.example.com -N
```

Spiegazione dei parametri:

- `-L 443:domain.example:443`: crea un tunnel locale sulla porta 443 che inoltra il traffico verso `domain.example:443`, attraverso il server remoto.
- `-N`: non esegue comandi remoti, limita la sessione al forwarding.

Nota: se la porta 443 è già occupata in locale, è possibile usare una porta alternativa (es. 8443) e specificarla manualmente nel browser.

### 3. Accesso al servizio

Aprire il browser e visitare:

```
https://domain.example
```

Poiché si utilizza un certificato valido per `domain.example` ma la risoluzione punta a `localhost`, il browser potrebbe mostrare un avviso di sicurezza. In tal caso, è sufficiente proseguire manualmente confermando l’eccezione.

## Verifica della connessione

È possibile eseguire alcuni test per confermare il corretto funzionamento della configurazione.

Verifica della risoluzione DNS:

```bash
ping domain.example
```

Verifica dell'accessibilità HTTPS:

```bash
curl -vk https://domain.example
```

Se tutto è configurato correttamente, il comando restituisce il contenuto HTML del servizio remoto.

## Esempio di script per Windows

Per semplificare l’avvio del tunnel, è possibile utilizzare il seguente script batch:

```bat
@echo off
echo Avvio tunnel SSH...
start "" ssh -L 443:domain.example:443 user@ssh.example.com -N
timeout /t 2
start https://domain.example
```

## Utilizzo su Linux e macOS

### 1. Modifica del file hosts

Aprire il file `/etc/hosts` con privilegi di root:

```bash
sudo nano /etc/hosts
```

Aggiungere la seguente riga in fondo al file:

```
127.0.0.1 domain.example
```

Salvare e chiudere l'editor.

### 2. Creazione del tunnel SSH

Aprire un terminale ed eseguire il comando:

```bash
ssh -L 443:domain.example:443 user@ssh.example.com -N
```

Come su Windows, se la porta 443 è già occupata è possibile utilizzare una porta alternativa (es. 8443) e specificarla nel browser con `https://domain.example:8443`.

### 3. Accesso al servizio

Aprire il browser e navigare all'indirizzo:

```
https://domain.example
```

### 4. Script opzionale per Linux/macOS

Per automatizzare l'apertura del tunnel e del browser, è possibile creare un semplice script shell:

```bash
#!/bin/bash
ssh -f -L 443:domain.example:443 user@ssh.example.com -N
sleep 2
xdg-open https://domain.example
```

Salvare questo contenuto in un file, ad esempio `start-tunnel.sh`, renderlo eseguibile e lanciarlo:

```bash
chmod +x start-tunnel.sh
./start-tunnel.sh
```

> Su macOS, è possibile usare `open` al posto di `xdg-open`.


## Note finali

Questa tecnica è utile in contesti aziendali o di laboratorio in cui l'accesso diretto a determinati servizi è limitato ma si dispone di un punto di uscita SSH. Può essere adattata per accedere a qualsiasi altro servizio web (come pannelli di controllo, interfacce di monitoraggio, portali interni).
