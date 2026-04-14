# Vatican Privé — Gestione Eventi

Questa repository contiene i dati degli eventi del Club Vatican Privé.  
È la **fonte unica di verità** per il sito web, il widget calendario e (in futuro) la dashboard membership.

---

## 📁 Struttura della repository

```
vatican-prive-events/
│
├── events.json          ← IL FILE CHE MODIFICHI TU
├── README.md            ← questa guida
└── archive/
    └── events_2025.json ← archivio eventi passati (opzionale)
```

---

## ✏️ Come aggiungere o modificare un evento

Apri `events.json` direttamente su GitHub (click sul file → matita in alto a destra).

### Struttura di un evento

```json
{
  "id": "evt_004",
  "title": "Nome dell'evento",
  "category": "arte",
  "date_start": "2026-07-10T20:00:00",
  "date_end":   "2026-07-10T23:00:00",
  "location": {
    "name": "Nome del luogo",
    "address": "Indirizzo completo, Roma",
    "maps_url": "https://maps.google.com/..."
  },
  "description_short": "Una riga evocativa — compare nella card.",
  "description_long": "Testo esteso — compare nel dettaglio evento.",
  "image_url": "https://link-alla-tua-immagine.jpg",
  "capacity": 20,
  "price_range": "€€€",
  "booking_url": "https://wa.me/39XXXXXXXXXX?text=...",
  "booking_type": "whatsapp",
  "status": "available",
  "featured": false,
  "google_calendar_event_id": ""
}
```

---

## 📌 Valori possibili per i campi principali

### `category`
| Valore | Etichetta visualizzata |
|--------|----------------------|
| `arte` | Arte & Cultura |
| `musica` | Musica |
| `gastronomia` | Gastronomia |
| `esclusivo` | Accesso Esclusivo |
| `benessere` | Benessere |
| `networking` | Networking |
| `esperienza` | Esperienze Uniche |

### `status`
| Valore | Significato |
|--------|-------------|
| `available` | Prenotabile — mostra il bottone booking |
| `sold_out` | Tutto esaurito — mostra badge rosso |
| `coming_soon` | Presto disponibile — nasconde il bottone |
| `cancelled` | Annullato — nasconde l'evento |

### `booking_type`
| Valore | Comportamento |
|--------|--------------|
| `whatsapp` | Apre WhatsApp con messaggio precompilato |
| `form` | Apre un form esterno (Tilda o altro) |
| `email` | Apre client email |
| `external` | Link esterno generico |

### `price_range`
`€` · `€€` · `€€€` · `su invito`

### `featured`
`true` = compare in evidenza nella hero del calendario  
`false` = compare nella lista normale

---

## 🔑 Come generare l'ID evento

Usa sempre il formato `evt_XXX` con numero progressivo:  
`evt_001`, `evt_002`, `evt_003`...

Non riutilizzare mai un ID già usato, anche per eventi cancellati.

---

## 🗓️ Sincronizzazione Google Calendar

Quando crei un evento anche su Google Calendar, copia l'**Event ID** di Google  
nel campo `google_calendar_event_id` dell'evento corrispondente nel JSON.

L'Event ID si trova in Google Calendar:  
*Apri evento → ⋮ menu → Pubblica evento → copia ID*

---

## ⚡ Il sito si aggiorna automaticamente?

**Sì.** Il widget sul sito legge questo file ogni volta che la pagina viene caricata.  
Non devi fare nulla dopo aver salvato le modifiche su GitHub — il sito è già aggiornato.

---

## 📞 Supporto tecnico

Per modifiche strutturali al JSON o al widget, contatta il team di sviluppo.
