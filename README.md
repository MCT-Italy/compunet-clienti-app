# compunet-clienti-app
Gestione clienti CompuNet

## File principali

| File | Descrizione |
|---|---|
| `Index.html` | App principale: dashboard clienti con mappa, statistiche, log modifiche |
| `CompuNet_Dashboard_TV.html` | Dashboard TV: mappa mondo in tempo reale, ottimizzata per display fisso |
| `setup_auth.sql` | Script SQL da eseguire una volta su Supabase per configurare l'autenticazione |

## Requisiti

- Connessione a Supabase (progetto `disbuelaedpqjyjiglqv`)
- Browser moderno con supporto ES2020+

## Setup iniziale

### 1. Configurazione autenticazione (obbligatorio al primo deploy)

Aprire il **SQL Editor** di Supabase ed eseguire il contenuto di `setup_auth.sql`.

Lo script:
- Abilita l'estensione `pgcrypto`
- Crea la tabella `app_auth` (non accessibile dall'esterno)
- Inserisce gli hash delle password per i ruoli `admin` e `viewer`
- Crea la funzione RPC `check_app_password` usata dal login

### 2. Avvio

Aprire `Index.html` direttamente nel browser oppure servire la cartella con un web server locale.

## Ruoli

| Ruolo | Permessi |
|---|---|
| `admin` | Lettura e scrittura su tutti i campi |
| `viewer` | Sola lettura |

## Cambio password

Per cambiare una password senza toccare il codice, eseguire su Supabase SQL Editor:

```sql
-- 1. Calcola il nuovo hash
SELECT encode(digest('nuova_password', 'sha256'), 'hex');

-- 2. Aggiorna (sostituire 'hash_qui' con il risultato del passo 1)
UPDATE app_auth SET pwd_hash = 'hash_qui' WHERE role = 'admin';
-- oppure WHERE role = 'viewer'
```

## Mappa clienti

Entrambe le dashboard mostrano i clienti sulla mappa mondiale divisi per fascia oraria:

- **Pallino verde**: backup cloud e PC entrambi attivi
- **Pallino arancio**: stato misto
- **Pallino rosso**: backup cloud e PC entrambi disattivati
- **Etichette rosse in basso** (area Patagonia): clienti senza nessun backup, con nome visibile

Le linee verticali separano le fasce orarie (UTC -12 → +12).
