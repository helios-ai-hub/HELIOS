# HELIOS in parole semplici

> Sintesi per chi non è tecnico. Dettagli e fonti nei documenti 00–05. Le stime sono ipotesi, non misure.

## 1. Di che cosa stiamo parlando
- **LIMET** è il nostro metodo di lavoro: dice in che ordine fare le cose, chi approva e cosa si documenta.
- **CEREBRO** è la "memoria di progetto": permette agli assistenti AI di consultare la nostra documentazione.
- **ECC** è un progetto esterno gratuito (licenza MIT) con una grande raccolta di assistenti AI, istruzioni e automatismi per programmare con Claude Code.
- **HELIOS** è l'idea di unire tutto: **«ECC con gli assistenti, guidato da LIMET»**, con un'unica dashboard web per gestire i tre mondi.

## 2. Possiamo farlo?
**Sì, a condizioni.** Non possiamo prendere ECC "così com'è": ha un suo modo di lavorare (pianifica, scrive, esegue test, fa commit) che va contro le nostre regole (l'assistente non lancia build/test e non fa commit da solo). Possiamo invece **prendere solo le parti utili** e farle lavorare dentro LIMET.

Condizioni principali:
1. Decidere formalmente di fare un'eccezione alla regola "niente strumenti di terzi".
2. Costruire prima una piccola prova (oggi gli assistenti di LIMET esistono solo sulla carta).
3. Far controllare a un legale alcune licenze.
4. Tutto deve funzionare su Windows e Unix.

## 3. Perché conviene
- Otteniamo subito **molto lavoro già fatto**: regole di buona pratica per linguaggio, controlli di qualità e sicurezza del codice.
- LIMET resta **il regista**: tracciabilità, approvazioni umane, nessuna azione automatica rischiosa.
- Un **unico progetto** (LIMET + CEREBRO + HELIOS) e **una dashboard** semplificano la manutenzione.

## 4. Perché non prendere tutto
- Molte funzioni di ECC **duplicano** quelle che abbiamo (pianificazione, memoria).
- Alcuni automatismi **bloccano o rallentano** il lavoro (in un test interno di ECC circa +45% di passaggi, senza beneficio dimostrato).
- Alcuni toccano **file personali** e inviano dati a servizi esterni.
- Il progetto ECC è curato da **una sola persona** e cambia molto in fretta.
- Alcune parti hanno **licenze diverse** o poco chiare.

## 5. Che cosa possiamo portare da ECC

| Parte di ECC | Esito | In parole semplici |
|---|---|---|
| Regole di scrittura per linguaggio (Java, TypeScript, Python…) | ✅ Sì | Testo puro, utile e sicuro |
| Revisori specializzati (sicurezza, database, qualità del codice) | ✅ Sì, adattati | Solo "leggono e commentano"; non eseguono nulla |
| Istruzioni (skill) puramente testuali, scelte una a una | ✅ Sì, previo controllo | Solo quelle verificate e con licenza chiara |
| Scanner di sicurezza AgentShield | 🔶 Forse, più avanti | Va prima verificato (dipendenze e licenza) |
| Assistenti che lanciano build, test, correzioni | ⚠️ Solo come testo | L'esecuzione resta allo strumento di LIMET |
| Pianificazione (`/plan`, PRD, pipeline) | ❌ Sostituita da LIMET | Abbiamo già un metodo migliore e tracciato |
| Memoria di ECC | ❌ No | Usiamo CEREBRO, più potente |
| Automatismi bloccanti (GateGuard ecc.) | ❌ No | Costano più di quanto danno |
| Servizi esterni (GitHub App, versione Pro, Itô, Nasiko) | ❌ No | Inviano dati fuori o richiedono account |
| Interfacce di ECC (Plan Canvas, Control Pane) | ❌ No | Le sostituisce la nostra dashboard |
| Contenuti con licenza dubbia o incompatibile | ❌ No | In attesa del parere legale |

## 6. Come lo facciamo (in breve)
1. **Decidere** le opzioni per ogni criticità (sono 27, ciascuna con soluzioni complete, in `05`).
2. **Inventariare** cosa prendere, con registro di licenze.
3. **Unire** LIMET, CEREBRO e HELIOS in un progetto, senza rompere quello che già usiamo.
4. **Provare** su un caso piccolo (prova di fattibilità) con criteri chiari di successo/stop.
5. **Portare** le parti scelte, **sostituire** la pianificazione di ECC con LIMET.
6. **Costruire la dashboard** React, prima in sola lettura.
7. **Testare** su Windows e Unix, **rafforzare**, **documentare**, **rilasciare gradualmente**.

## 7. Impegno e rischi
- **Impegno stimato:** circa 69–107 giorni-persona (ipotesi).
- **Rischi principali:** assistenti LIMET ancora da costruire; licenze da verificare; sicurezza della dashboard; ECC che cambia nel tempo (lo "congeliamo" a una versione precisa).

## 8. Cosa serve da te, SUPERVISORE
- Confermare l'eccezione alla regola sugli strumenti di terzi.
- Scegliere le opzioni (di default proponiamo quelle consigliate ★ in `05` §3).
- Autorizzare la prova di fattibilità e il controllo legale.
