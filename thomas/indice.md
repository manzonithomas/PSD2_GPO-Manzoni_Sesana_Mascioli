# PSD2 — Parte Tecnica e Sicurezza (Studente 2)

---

## 1. Strong Customer Authentication (SCA)

- I 3 fattori di autenticazione: Conoscenza, Possesso, Inerenza
- Indipendenza tra i fattori (compromettere uno non compromette l'altro)
- Esempi concreti: password, smartphone, impronta digitale / Face ID
- Evoluzione: da OTP via SMS a notifiche push firmate digitalmente

---

## 2. Dynamic Linking

- Cos'è: il codice di autenticazione è legato crittograficamente all'importo e al beneficiario
- Se qualcuno intercetta il codice, non può usarlo per un importo o destinatario diverso
- 3 garanzie: consapevolezza del pagatore / integrità della transazione / unicità del codice

---

## 3. 3D Secure 2.0

- Cos'è 3DS 2.0 e perché è nato (limite della versione 1.0)
- Tabella confronto 3DS 1.0 vs 3DS 2.0:
  - Integrazione (browser vs nativa SDK)
  - Dati scambiati (15 elementi vs 150+)
  - Autenticazione (password statica vs biometria)
  - User experience (friction vs frictionless)
  - Analisi del rischio (statica vs Machine Learning)
- Risk-Based Authentication (RBA): se il rischio è basso, pagamento frictionless

---

## 4. Esenzioni SCA

- Pagamenti e-commerce sotto 30€ (max cumulativo 100€ o 5 operazioni)
- Pagamenti contactless fino a 50€ (max 150€ cumulativi)
- Abbonamenti ricorrenti (SCA solo alla prima transazione)

---

## 5. API e Open Banking

- Cos'è un'API bancaria: come il software di una Fintech "parla" con una banca
- Architettura REST + formato JSON
- 3 livelli di sicurezza della comunicazione:
  1. Certificati eIDAS (QWAC + QSealC)
  2. Canale crittografato mTLS (autenticazione reciproca)
  3. Standard FAPI (protezione avanzata rispetto a OAuth standard)

---

## 6. OAuth 2.0 — Il Protocollo di Autorizzazione

- Obiettivo: autorizzare una Fintech senza mai condividere username e password della banca
- I 5 passi del flusso:
  1. Redirect dell'utente alla pagina della sua banca (ASPSP)
  2. Login + SCA + conferma consenso presso la banca
  3. La banca restituisce un Authorization Code alla Fintech
  4. La Fintech scambia il codice per un Access Token + Refresh Token
  5. La Fintech usa l'Access Token per le chiamate API successive
- Tabella dei token:
  - Authorization Code → < 10 minuti
  - Access Token → 30-60 minuti
  - Refresh Token → giorni/mesi (con SCA obbligatoria ogni 90-180 giorni)

---

## 7. AISP vs PISP

- **AISP** (Account Information Service Provider): API di *lettura*
  - Legge saldo e movimenti con consenso dell'utente
  - Può aggiornarsi fino a 4 volte al giorno in background senza ripetere la SCA
  - Esempio: app che aggrega tutti i conti in un'unica schermata

- **PISP** (Payment Initiation Service Provider): API di *scrittura*
  - Avvia pagamenti direttamente dal conto dell'utente
  - Richiede Dynamic Linking: l'API deve ricevere IBAN e importo, mostrati all'utente durante la SCA
  - Esempio: pagare su un e-commerce direttamente dal conto, senza carta

---

## 8. Flusso Completo di un Pagamento Online (Step-by-Step)

- **Step 1 — Tokenizzazione**: i dati della carta non toccano mai i server del merchant (iframe del gateway → token univoco)
- **Step 2 — AReq**: il merchant contatta l'acquirer → directory server → Access Control Server (ACS) della banca emittente. Vengono inviati i metadati per la Risk-Based Authentication
- **Step 3 — Frictionless vs Challenge**: l'ACS analizza il rischio
  - Rischio basso → ARes positiva, nessuna interruzione per l'utente
  - Rischio alto → richiesta di verifica aggiuntiva (SCA)
- **Step 4 — SCA + Dynamic Linking**: notifica push sullo smartphone, l'utente autentica con biometria. Il dispositivo firma crittograficamente importo + nome merchant
- **Step 5 — Autorizzazione finale**: la banca blocca l'importo e invia conferma al gateway. Tutto in meno di 5 secondi

---

## 9. Rischi Tecnologici

### Phishing 2.0 / Adversary-in-the-Middle (AitM)
- Non più un sito falso, ma un **proxy trasparente** tra l'utente e la vera banca (es. EvilProxy)
- 4 fasi: Lure → Proxying → Intercettazione SCA in tempo reale → Session Hijacking
- Obiettivo finale: il session token/cookie già autenticato, non la password

### SIM Swap
- Sfrutta le debolezze dei gestori telefonici per "rubare" il numero di telefono
- 4 fasi: Raccolta dati personali → Impersonificazione del provider → Acquisizione della SIM → Svuotamento del conto con bonifici istantanei
- Perché è pericoloso: rende inutile l'SMS come secondo fattore SCA

### Social Engineering / Deepfake Vishing
- Clonazione vocale tramite AI per impersonare funzionari bancari
- L'utente viene convinto a "annullare una frode" confermando invece un pagamento reale

---

## 10. Conclusione (passaggio allo Studente 3)

> *"La PSD2 ha trasformato la banca da una fortezza chiusa a un nodo di rete aperto.  
> I protocolli crittografici sono quasi inviolabili — ma l'anello debole resta l'utente.  
> La sfida futura non è solo cifrare i dati, ma renderli resistenti a un crimine informatico sempre più industrializzato."*

- Direzione PSD3: abbandono degli SMS, miglioramento delle API, verifica del beneficiario (Confirmation of Payee)