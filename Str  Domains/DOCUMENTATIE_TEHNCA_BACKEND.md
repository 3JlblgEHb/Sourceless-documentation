# 📚 DOCUMENTAȚIE TEHNICĂ COMPLETĂ
## Sourceless E-Commerce Backend - Specificație Tehnică Completă

**Proiect:** Sourceless E-Commerce Backend Main  
**Limbă:** TypeScript / Node.js  
**Platformă:** NestJS + TypeORM + MySQL

---

## 📋 CUPRINS

1. [Prezentarea generală a proiectului](#prezentarea-generală-a-proiectului)
2. [Stiva tehnologică](#stiva-tehnologică)
3. [Arhitectura aplicației](#arhitectura-aplicației)
4. [Structura bazei de date](#structura-bazei-de-date)
5. [API Endpoints](#api-endpoints)
6. [Autentificare și autorizare](#autentificare-și-autorizare)
7. [Module ale aplicației](#module-ale-aplicației)
8. [Flux principal de afaceri](#flux-principal-de-afaceri)
9. [Integrări și servicii externe](#integrări-și-servicii-externe)
10. [Deployment și DevOps](#deployment-și-devops)
11. [Logging și monitorizare](#logging-și-monitorizare)
12. [Securitate](#securitate)
13. [Testare](#testare)
14. [Migrații bază de date](#migrații-bază-de-date)

---

## 🎯 PREZENTAREA GENERALĂ A PROIECTULUI

### Descriere
**Sourceless E-Commerce Backend** - aceasta este o aplicație server completă pentru gestionarea comerțului electronic cu suport pentru:
- 🔐 Gestionarea domeniilor și utilizatorilor
- 💳 Sistemul de plăți (Coinbase, CoinPayments, Authorize.net)
- 🛒 Coș de cumpărături și finalizarea comenzilor
- 🎫 Sistemul de vouchere și reduceri
- 👤 Program de afiliat și chei API
- 🖼️ Funcționalitate NFT cu generarea imaginilor
- 💰 Gestionarea portofelelor și tranzacțiilor
- 👥 Gestionarea rolurilor și permisiunilor

### Caracteristici cheie
- **Arhitectură gata pentru microservicii** - disponibilitate pentru microservicii
- **Design bazat pe evenimente** - arhitectură orientată pe evenimente
- **Control acces bazat pe roluri (RBAC)** - gestionarea accesului pe bază de roluri
- **Suport pentru mai multe porți de plată** - suport pentru mai multe sisteme de plată
- **Documentație API** - documentație completă a API-ului prin Swagger
- **Logging cuprinzător** - înregistrare detaliată a tuturor operațiilor
- **Migrații bază de date** - gestionarea migrațiilor BD
- **Suport Docker** - suport pentru containerizare

---

## 🛠️ STIVA TEHNOLOGICĂ

### Framework Backend
```
NestJS 10.x          - Framework progresiv Node.js
TypeScript 5.x       - JavaScript tastat
Express              - Server HTTP (încorporat în NestJS)
```

### Bază de date
```
MySQL 8.x / MariaDB  - BD relațională
TypeORM 0.3.x       - ORM pentru lucrul cu BD
Migration Tools      - Versionare BD
```

### Autentificare și securitate
```
JWT (JSON Web Tokens)         - Autentificare bazată pe tokeni
Passport.js                   - Strategie de autentificare
Argon2                        - Hashing parole
bcrypt                        - Hashing suplimentar
cors                          - Cross-Origin Resource Sharing
express-session               - Gestionare sesiuni
```

### API și documentație
```
Swagger/OpenAPI 3.0  - Documentație API interactivă
swagger-ui-express   - UI pentru documentație
Reflect-metadata     - Suport decoratori
class-transformer    - Transformare date
class-validator      - Validare date
```

### Integrări plăți
```
Coinbase Commerce    - Plăți cripto
CoinPayments         - Plăți cripto
Authorize.net        - Plăți tradiționale
```

### Blockchain și Web3
```
Web3.js 4.x         - Interacțiune cu Ethereum
Ethers.js 6.x       - Bibliotecă auxiliară
```

### Utilitare
```
Resend              - Trimitere email
Winston              - Logging
Cache-Manager        - Caching
UUID                 - Generare ID unice
Canvas               - Generare imagini (NFT)
Axios                - Client HTTP
Form-Data            - Lucru cu formuri
```

### Instrumente dezvoltare
```
Jest                 - Framework testare
ts-node             - Execuție TypeScript
ESLint & Prettier   - Analiză statică și formatare
Husky               - Git hooks
```

---

## 🏗️ ARHITECTURA APLICAȚIEI

### 1. Arhitectură stratificată

```
┌─────────────────────────────────────────┐
│         REST API / HTTP Layer           │
│     Controllers + Swagger Documentation │
└─────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────┐
│      Application Layer (NestJS)         │
│  Services, DTOs, Middleware, Guards     │
└─────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────┐
│      Domain Layer (Business Logic)      │
│  Entities, Repositories, Domain Events  │
└─────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────┐
│   Infrastructure Layer (Data Access)    │
│  TypeORM, Database, External Services   │
└─────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────┐
│        Data Persistence Layer           │
│     MySQL Database + Cache (Redis)      │
└─────────────────────────────────────────┘
```

### 2. Structura modulară

Proiectul este organizat în module NestJS independente:
- **auth** - Autentificare și autorizare
- **domains** - Gestionare domenii
- **cart** - Coș de cumpărături
- **checkout** - Procesare plăți
- **coinbase** - Integrare Coinbase
- **coinpayments** - Integrare CoinPayments
- **nft** - Funcționalitate NFT
- **voucher** - Sistemul de vouchere
- **api-key** - Gestionare chei API
- **domain-search** - Căutare domenii

---

## 🗄️ STRUCTURA BAZEI DE DATE

### Entități principale

**Utilizatori și control acces:**
- AdminUser - Administratori
- Buyer - Cumpărători
- Role - Roluri
- Permission - Permisiuni

**Domenii și comerț:**
- Domain - Domenii în catalog
- Cart - Coșuri de cumpărături
- CartDomain - Articole în coș
- Transaction - Tranzacții
- SoldDomain - Domenii vândute

**Finanțare:**
- Wallet - Portofelele cumpărătorilor
- Voucher - Coduri de reducere
- ApiKey - Chei API
- Affiliate - Programe de afiliere

---

## 🌐 API ENDPOINTS

**Notă:** Descrierea completă a tuturor endpoints-urilor API și formate de răspuns se găsește în Swagger la:
```
http://localhost:4000/swagger
```

Documentația API este generată automat și este sempre sincronizată cu codul actual.

---

## 🔐 AUTENTIFICARE ȘI AUTORIZARE

### JWT (JSON Web Tokens)

Tokeni au 15 minute validitate, cu opțiune refresh pentru 7 zile.

**Structură:** Header.Payload.Signature

### Roluri și permisiuni

Ierarhia:
```
SUPER_ADMIN → ADMIN → MODERATOR / SUPPORT
VENDOR → AFFILIATE
```

Fiecare rol are permisiuni specifice pentru:
- Domenii (criere, citire, actualizare, ștergere)
- Utilizatori (gestionare)
- Plăți (gestionare)
- Analiză (vizualizare)
- Setări (gestionare)

### API Key Autentificare

Cheile API sunt hashed și pot fi rotite pentru siguranță.

---

## 📦 MODULE ALE APLICAȚIEI

### 1. Modul Auth (Autentificare)

**Funcționalitate:**
- Înregistrare administratori cu email/parolă
- Înregistrare cumpărători cu date personale
- Conectare cu verificare parolă
- Generare tokeni JWT (acces 15 min, refresh 7 zile)
- Reîmprospătare sesiuni
- Dezactivare conturi
- Gestionare roluri și permisiuni

**Flux utilizator:**
1. Cumpărătorul se înregistrează → parolă hashuită (Argon2) → cont BUYER activ
2. Administrator se conectează → verificare email/parolă → primește JWT token
3. Token folosit în header-ul oricărei cereri API
4. După expirare, refresh token reînnoire

**Statuse conturi:** ACTIVE, INACTIVE, SUSPENDED

---

### 2. Modul Domenii

**Funcționalitate:**
- Adăugare domenii în catalog cu preț
- Editare preț și informații domeniu
- Ștergere domenii
- Căutare după nume/extensie
- Filtrare după preț (min-max)
- Sortare după preț/nume
- Paginare (20 per pagină default)
- Istoric schimbări preț

**Statuse domeniu:**
- **AVAILABLE** - Disponibil pentru cumpărare
- **RESERVED** - Cumpărător în coș (max 24h)
- **SOLD** - Deja vândut
- **EXPIRED** - Expirație apropie (contact pentru reînnoire)

**Exemplu flux:**
- Admin adaugă domain.ro la $50
- Cumpărător caută "domain" → vede listă cu preț
- Selectează domain → stare devine RESERVED
- După 24h fără plată → redevine AVAILABLE
- După plată → stare SOLD

---

### 3. Modul Coș (Shopping Cart)

**Funcționalitate:**
- Creare coș automat per cumpărător
- Adăugare/ștergere domenii din coș
- Calcul subtotal (suma preți)
- Aplicare vouchere reducere
- Calcul total (subtotal - reducere)
- Expirare automată după 24 ore inactivitate
- Clearare coș manual

**Calcul preț:**
```
Subtotal = suma preț domenii
Reducere = voucher.discount (% sau suma fixă)
Total = Subtotal - Reducere
```

**Exemplu:**
- Adaugă domain1.ro ($50) + domain2.ro ($30) = Subtotal $80
- Aplică voucher "SAVE20" (-20%) = Reducere $16
- Total de plată: $64

---

### 4. Modul Checkout (Procesare plăți)

**Funcționalitate:**
- Inițiere tranzacție din coș
- Selectare metodă plată (Coinbase, CoinPayments, etc.)
- Redirecționare la furnizor plată
- Verificare status plată după completare
- Confirmare tranzacție cu stocare în bază
- Notificare pe email (succes/eșec)

**Flux plată:**
1. Cumpărător apasă "Checkout"
2. Sistem creează Transaction (status: PENDING)
3. Redirecționare la Coinbase/CoinPayments
4. După plată, webhook confirmă status
5. Transaction marcată CONFIRMED, domenii marcate SOLD

**Statuse tranzacție:**
- PENDING - Așteptare plată
- CONFIRMED - Plată primită, domenii active
- FAILED - Plată eșuată
- CANCELLED - Anulată de utilizator

---

### 5. Modul Coinbase Commerce

**Funcționalitate:**
- Creare "charge" (cerere de plată)
- Așteptare confirmări blockchain (0-3 confirmări)
- Procesare webhooks din Coinbase
- Obținere rate schimb BTC/USD în timp real
- Suport mai mult criptomonede (BTC, ETH, USDC)

**Avantaje Coinbase:**
- Nu ținem fonduri (instant settlement pe blockchain)
- Rate schimb automate zilnic
- Securitate blockchain
- Suport multiple monede

---

### 6. Modul CoinPayments

**Funcționalitate:**
- Alternativă la Coinbase (mai mult criptomonede)
- Criere tranzacții cu conversie automată preț
- Primire notificări IPN (Instant Payment Notifications)
- Verificare plată pe platforma CoinPayments

---

### 7. Modul NFT (Certificate digitale)

**Funcționalitate:**
- Generare certificat digital pentru fiecare domeniu cumpărat
- Criere imagine PNG cu logo + date domeniu
- Metadate NFT (proprietar, dată, ID blockchain)
- Publicare pe IPFS (stochaj descentralizat)
- Link către blockchain (dacă se implementează)

**Exemplu:** Cumpărătorului îi se generează imagine cu "domain.ro Owner Certificate 2025"

---

### 8. Modul API Key (Chei programare)

**Funcționalitate:**
- Generare chei unice pentru integrări externe
- Fiecare cheie cu permisiuni (READ, WRITE, DELETE)
- Expirare automată după 1 an
- Abilitate rotație (dezactivare veche, criere nouă)
- Urmărire utilizare (cate requeste/zi)

**Exemplu:** Clientul extern primește API key pentru citire liste domenii

---

### 9. Modul Voucher (Coduri reducere)

**Funcționalitate:**
- Criere coduri reducere (SAVE20, HOLIDAY50, etc.)
- Procent reducere (20%, 50%) sau sumă fixă ($10)
- Dată start/expirare (ex: valabil 1 martie - 31 martie)
- Limită utilizări (ex: max 100 utilizări)
- Limită minimă coș (ex: min $50 pentru a folosi)
- Aplicabil pe anumite domenii sau toate

**Exemplu:**
- Cod "CYBER2025" = 30% reducere, valabil 1 dec - 31 dec 2025, max 500 utilizări

---

### 10. Modul Căutare Domenii

**Funcționalitate:**
- Căutare domenii disponibile extern
- Integrare WHOIS API (GoDaddy, NameCheap)
- Verificare pe registre diferite
- Afișare disponibilitate și preț estimat
- Recomandare domenii similare

---

## 🔄 FLUX PRINCIPAL - Cumpărare Domeniu

```
1. CUMPĂRĂTOR se înregistrează
   ↓
2. Caută domeniu → Vede preț și disponibilitate
   ↓
3. Adaugă în coș → Domeniu stare RESERVED
   ↓
4. Aplică voucher (opțional) → Calcul reducere
   ↓
5. Apasă Checkout → Creează Transaction (PENDING)
   ↓
6. Redirecție Coinbase → Plată cu BTC/ETH
   ↓
7. Confirmă blockchain → Webhook notificare
   ↓
8. Transaction status → CONFIRMED
   ↓
9. Email notificare → "Domeniu cumpărat!"
   ↓
10. NFT certificate generat + IPFS
```

---

## 💼 INTEGRĂRI ȘI SERVICII EXTERNE

### 1. Coinbase Commerce
- **API Endpoint**: https://api.commerce.coinbase.com
- **Variabile mediu**: COINBASE_API_KEY
- **Use Cases**: Plăți criptomonede

### 2. CoinPayments
- **API Endpoint**: https://www.coinpayments.net/api.php
- **Variabile mediu**: COINPAYMENTS_API_KEY, COINPAYMENTS_API_SECRET
- **Use Cases**: Plăți multiple criptomonede

### 3. Authorize.net
- **API Endpoint**: https://api.authorize.net/xml/v1/request.api
- **Variabile mediu**: AUTHORIZE_API_LOGIN_ID, AUTHORIZE_TRANSACTION_KEY
- **Use Cases**: Plăți cu card de credit

### 4. Email Service (Resend)
- **API Endpoint**: https://api.resend.com
- **Variabile mediu**: RESEND_API_KEY
- **Use Cases**: Trimitere emailuri de confirmare, notificări

### 5. WHOIS / Domain Search
- **Furnizori**: GoDaddy, NameCheap, Domain.com APIs
- **Use Cases**: Verificare disponibilitate domenii

---

## 🚀 DEPLOYMENT ȘI DEVOPS

### Configurare Docker

**Dockerfile (Producție)**
```dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
RUN npm run build

FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY package.json .
EXPOSE 4000
CMD ["npm", "run", "start:prod"]
```

### Variabile mediu

**.env.production**
```bash
NODE_ENV=production
DB_HOST=prod-mysql-host
DB_PORT=3306
DB_USER=${PROD_DB_USER}
DB_PASSWORD=${PROD_DB_PASSWORD}
DB_NAME=${PROD_DB_NAME}
JWT_SECRET=${PROD_JWT_SECRET}
PORT=4000
APP_URL=https://api.example.com
```

---

## 📊 LOGGING ȘI MONITORIZARE

### Winston Logger

Loguri se salvează în:
- Console (dezvoltare)
- Fișiere zilnice (producție)
- Rotație automată după 20MB
- Păstrare 14 zile

---

## 🔒 SECURITATE

### Validare intrare
- Email validation
- Parole minimum 8 caractere
- XSS protection
- CSRF protection

### Hashing parolă
- Argon2 pentru parole noi
- Bcrypt ca fallback

### Configurare CORS
```
Originile permise: http://localhost:3000
Credentials: true
```

### Rate Limiting
- 10 requeste pe minut per IP
- 100 requeste pe oră per utilizator

---

## 🧪 TESTARE

### Unit teste (Jest)
```bash
npm run test           # Unit teste
npm run test:e2e       # E2E teste
npm run test:cov       # Teste cu acoperire
```

---

## 📁 MIGRAȚII BAZĂ DE DATE

### Criere migrație
```bash
npm run migration:generate src/migrations/CreateAdminUsers
npm run migration:create AddFieldToAdminUsers
```

### Rulare migrații
```bash
npm run typeorm migration:run -d src/data-source.ts
npm run migration:revert
```

---

## 🔄 WORKFLOW DEZVOLTARE

### Pornire dezvoltare
```bash
npm install
npm run start:dev
# http://localhost:4000
# Swagger: http://localhost:4000/swagger
```

### Build producție
```bash
npm run build
npm run start:prod
```

---

## 📞 SUPORT ȘI DEPANARE

### Probleme comune

**Eroare conexiune bază de date:**
- Verificați dacă MySQL este activ
- Verificați variabilele .env
- Reporniți containerul Docker

**Token JWT expirat:**
- Utilizați refresh token
- POST /auth/refresh-token cu refreshToken

**Eroare CORS:**
- Verificați ALLOWED_ORIGINS în .env
- Adăugați origin în configurare cors

---

## 📈 MONITORIZARE ȘI SCALARE

### Health Check
```
GET /health
```

### Recomandări scalare
- Scalare orizontală cu load balancer
- Read replicas pentru MySQL
- Redis pentru caching
- Bull / RabbitMQ pentru taskuri asincrone

---

## 📚 RESURSE SUPLIMENTARE

### Documentație oficială
- [NestJS Documentation](https://docs.nestjs.com)
- [TypeORM Documentation](https://typeorm.io)
- [Passport.js Documentation](http://www.passportjs.org)

### Instrumente testare API
- **Postman**: https://www.postman.com
- **Insomnia**: https://insomnia.rest
- **Thunder Client**: VS Code extension

---



## Contact
Pentru întrebări suplimentare: 3JlblgEHb88@gmail.com