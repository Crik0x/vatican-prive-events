# CLAUDE.md — Vatican Privé · Repository `vatican-prive-site`
> Istruzioni permanenti per Claude Code · Aggiornato: Aprile 2026
> Leggi questo file integralmente prima di qualsiasi operazione sul repository.

---

## 1. CONTESTO DEL PROGETTO

**Vatican Privé** è un club culturale privato con sede in Via della Conciliazione 44, 00193 Roma.
**Fondatore:** Nicolaj D'Ortona (firma tutte le comunicazioni come "Nicolaj", mai come "il club" o in terza persona).
**Network:** Club Privé — il franchising internazionale di club culturali fondato da Nicolaj.
**Stack:** Tilda (CMS) + GitHub (dati) + Supabase (database) + n8n (automazioni) + Brevo (email) + Stripe (pagamenti).

---

## 2. VERITÀ ASSOLUTE — NON RIMETTERE MAI IN DISCUSSIONE

- Livello III membership = **Costruttore** — MAI "Architetto" (errore storico)
- MAI usare "prezzo pubblico" per i soci — sempre **"prezzo Osservatore"**
- Font: **Cinzel + Cormorant Garamond + Lato** — MAI Inter/Roboto/Arial
- Colori: MAI `#000000` né `#FFFFFF` — usa sempre la palette definita
- URL eventi: sempre `/eventi/{city}/{format-slug}` — mai altro pattern
- Sede flagship: **Via della Conciliazione 44, 00193 Roma**
- Il network si chiama **Club Privé** — MAI "Salotto dei Damascati" come nome del franchising
- Il Salotto dei Damascati è un **movimento culturale e prodotto del club** — non è il franchising
- Ogni sede: **{Città} Privé** — eccetto Roma che resta **Vatican Privé**
- La membership è **globale** — vale in tutti i club del network
- MAI usare "franchise" nel copy pubblico — usare "rete di club", "sedi affiliate", "il network"

---

## 3. STRUTTURA DEL REPOSITORY

```
vatican-prive-site/
├── CLAUDE.md                    ← questo file
├── events.json                  ← sorgente unica di verità — calendario attivo
├── events_archive.json          ← eventi completati (non carica il sito)
├── generate-markup.js           ← genera JSON-LD schema markup
├── .github/
│   └── workflows/
│       └── update-markup.yml    ← GitHub Action — si attiva ad ogni push
└── README.md
```

**URL raw eventi (widget Tilda legge questo):**
```
https://raw.githubusercontent.com/Crik0x/vatican-prive-site/refs/heads/main/events.json
```

---

## 4. REGOLE PER events.json

### Cosa toccare spesso
- Sezione `events` — aggiungi, aggiorna disponibilità, chiudi eventi passati

### Cosa toccare raramente
- Sezione `formats` — solo se cambi struttura/prezzo permanente di un format
- Sezione `cities` — solo all'apertura di nuove sedi
- Sezione `pricing_tiers` — solo se cambi le quote membership

### Regole tassative
- `id` eventi: sempre progressivo `evt_001`, `evt_002` ecc. — **mai riutilizzare un ID**
- `format_id`: sempre `snake_case` — mai trattini, mai spazi
- Date: sempre ISO 8601 senza offset — `"2026-05-10T20:00:00"` ✓ — mai `+02:00`
- `last_updated`: aggiornalo **sempre** prima di ogni commit
- Livello III: sempre `"costruttore"` — mai `"architetto"`

### format_id validi
```
salotto_damascati · aperitivi_divini · menti_salotto · scacchiera_re
conclave · cena_membri · fuori_mura · cash_flow · business_meeting
```

### Valori status validi per gli eventi
```
available · sold_out · waitlist · coming_soon · cancelled · completed
```

### Flusso archivio
Quando un evento ha `"status": "completed"`, spostalo in `events_archive.json`.
Non cancellare mai un evento — spostalo sempre nell'archivio.

---

## 5. FLUSSO GIT — DA SEGUIRE SEMPRE

```bash
# 1. Modifica il file
# 2. Aggiorna last_updated in events.json
# 3. Commit e push
git add events.json                          # o altri file modificati
git commit -m "Descrizione chiara e breve"
git push
# 4. Verifica GitHub Actions (tab Actions su GitHub)
# 5. Il sito si aggiorna entro 60 secondi dal push
```

**Formato messaggi commit:**
```
feat: Aggiunto evt_009 — Aperitivi diVini 10 maggio
fix: Corretto available evt_008 — 2 prenotazioni confermate
arch: Spostati eventi completati aprile in archive
update: Aggiornato last_updated e status evt_003
```

---

## 6. SISTEMA PRENOTAZIONI — ARCHITETTURA

### Stack prenotazioni
```
Form HTML (embed Tilda)
    ↓
Supabase — tabella bookings
    ↓
n8n — 5 workflow (vedi sotto)
    ↓
Brevo (email conferma) + Telegram (notifica Nicolaj)
    ↓
Stripe (pagamento online) / Bonifico (pagamento manuale)
```

### Tabelle Supabase principali
- `members` — soci del club con livello membership
- `bookings` — prenotazioni (confirmed / pending_payment / waitlist / cancelled)
- `events_catalog` — mirror degli eventi da events.json
- `participations` — storico partecipazioni per socio

### Logica soci vs non soci
- **Socio** → verifica email in `members` → nessun pagamento anticipato → prenota con conferma diretta
- **Non socio** → sceglie Stripe (immediato) o Bonifico (manuale) → prenotazione confermata solo dopo pagamento

### Flussi n8n (5 workflow)
1. Non socio + Stripe → webhook pagamento → conferma automatica
2. Non socio + Bonifico → email con IBAN → attesa conferma manuale → conferma
3. Socio → verifica membership → conferma diretta → email + notifica Telegram
4. Sold out → salva in waitlist → notifica automatica se si libera un posto
5. Cancellazione → libera posto → offre a primo in waitlist

---

## 7. PALETTE COLORI (da usare in tutto il codice frontend)

```css
--oro:          #C9A96E;   /* CTA primarie, accenti */
--oro-scuro:    #8B6914;   /* label, eyebrow text */
--oro-c:        #F5ECD8;   /* sfondi card premium */
--ink:          #1A1814;   /* testi principali, sfondi dark */
--ink-mid:      #3A3530;   /* testi secondari */
--ink-soft:     #6B6458;   /* testi terziari, note */
--carta:        #FAFAF7;   /* sfondo pagine chiare — MAI bianco puro */
--pergamena:    #F5F0E8;   /* sfondi sezioni alternate */
--rosso:        #8B1A1A;   /* badge esclusivi, alert — con parsimonia */
```

---

## 8. MEMBERSHIP — RIFERIMENTO RAPIDO

| Livello | Nome | Quota annuale | Sconto eventi | VP benvenuto |
|---|---|---|---|---|
| I | Osservatore | €250 | — | — |
| II | Anima del Club | €600 | −15% | 50 VP (90gg) |
| III | **Costruttore** | €960 | −33% | 150 VP (90gg) |

**Prezzi eventi (Osservatore = base):**
| Format | Osservatore | Anima | Costruttore |
|---|---|---|---|
| Il Conclave | €250 | €212 | €168 |
| Menti in Salotto | €120 | €100 | €80 |
| Aperitivi diVini | €45 | €38 | €30 |
| Verba Manent | €80 | €68 | €54 |
| Cash Flow | €75 | €60 | €50 |
| Scacchiera dei Re | €20 | €17 | €14 |
| Business Meeting | €35 | €30 | €24 |
| Fuori le Mura | €180–300 | €155–255 | €120–201 |
| Cena dei Membri | inclusa (1–3/anno) | inclusa | inclusa |
| Salotto Damascati | gratuito | gratuito | gratuito |

---

## 9. TONE OF VOICE — REGOLE ESSENZIALI

### Sempre
- Parla come un padrone di casa colto — non come un buttafuori
- Usa "serate", "incontri", "conversazioni" — mai "eventi", "sessioni", "meeting"
- Ogni testo deve avere un dettaglio concreto che evoca l'esperienza
- Firma sempre con "Nicolaj" — mai "il club" o "lo staff"

### Mai
- "Vatican Privé non è per tutti" → usa "Vatican Privé è per chi riconosce il valore del tempo condiviso"
- "Siamo presenti in 8 città" → usa "Da Roma a Berlino, lo stesso silenzio prima di una buona conversazione"
- "Il nostro franchise" → usa "la nostra rete di club" o "le sedi affiliate"
- Emoji nei testi ufficiali del club

---

## 10. OPERAZIONI COMUNI — COMANDI RAPIDI

### Aggiungere un evento
> "Aggiungi un nuovo Aperitivi diVini per [data], [N] posti, disponibilità [N]"

### Aggiornare disponibilità
> "Mario Rossi ha prenotato evt_008 — aggiorna available"

### Chiudere eventi passati
> "Sposta tutti gli eventi con date precedenti a oggi e status 'completed' in events_archive.json"

### Generare copy evento
> "Scrivi il testo per la card Instagram dell'Aperitivi diVini del [data] — tone of voice Vatican Privé"

### Verificare stato prenotazioni
> "Quanti posti restano per ogni evento pubblicato in events.json?"

### Aggiornamento mensile
> "Aggiungi tutti gli eventi di [mese] seguendo il calendario mensile tipo del master v3"

---

## 11. CONTATTI E RIFERIMENTI

| Campo | Valore |
|---|---|
| Fondatore | Nicolaj D'Ortona |
| Email | info@vaticanprive.com |
| Telefono / WhatsApp | +393203194044 |
| Sito | https://www.vaticanprive.com |
| Repository | github.com/Crik0x/vatican-prive-site |
| Supabase project | da configurare |
| n8n instance | Railway — da configurare |
| Brevo account | da configurare |
| Stripe account | da configurare |

---

## 12. FILE DI RIFERIMENTO MASTER

Il documento strategico completo è **VATICAN_PRIVE_MASTER_v3.md**.
In caso di conflitto tra questo CLAUDE.md e il master v3, **il master v3 ha sempre la precedenza**.
Allega il master v3 alle conversazioni Claude quando lavori su brand, copy o strategia.

---

*Vatican Privé — Club Culturale Privato*
*Club Privé Network — Una tessera. Il mondo.*
*CLAUDE.md — uso interno · Aprile 2026*
