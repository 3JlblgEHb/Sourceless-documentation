# Documentație Tehnică - STR Domains NFT Marketplace DApp

## Cuprins

1. [Prezentare Generală](#prezentare-generală)
2. [Structura Proiectului](#structura-proiectului)
3. [Tehnologii și Dependențe](#tehnologii-și-dependențe)
4. [Configurare și Setup](#configurare-și-setup)
5. [Arhitectura Aplicației](#arhitectura-aplicației)
6. [Componente Principale](#componente-principale)
7. [Contextul și Gestionarea Stării](#contextul-și-gestionarea-stării)
8. [Servicii și SDK](#servicii-și-sdk)
9. [Contracte Inteligente](#contracte-inteligente)
10. [Funcționalități Detaliate](#funcționalități-detaliate)
11. [Ghid de Dezvoltare](#ghid-de-dezvoltare)
12. [Mentenanță și Troubleshooting](#mentenanță-și-troubleshooting)
13. [Securitate și Best Practices](#securitate-și-best-practices)

---

## 1. Prezentare Generală

### Scopul Proiectului

STR Domains NFT Marketplace DApp este o aplicație web descentralizată (DApp) construită cu React și TypeScript, care permite utilizatorilor să:
- Conecteze portofelul digital (MetaMask sau WalletConnect)
- Navigheze și cumpere NFT-uri ale domeniilor STR
- Să-și vizualizeze propriile domenii
- Să listeze domeniile pentru vânzare
- Să gestioneze redevențele de vânzare
- Să mintuiască noi domenii

### Rețeaua Blockchain

Aplicația funcționează pe **Polygon Amoy Testnet**:
- **Chain ID:** 80002
- **RPC URL:** https://rpc-amoy.polygon.technology
- **Block Explorer:** https://amoy.polygonscan.com
- **Monedă nativă:** MATIC (18 decimale)

### Stadiul Proiectului

- ✅ Conexiunea portofelului (MetaMask, WalletConnect)
- ✅ Afișarea stării rețelei
- ✅ Marketplace - vizualizare și cumpărare NFT-uri
- ✅ Pagina domeniilor personale
- ✅ Gestionarea listelor de vânzare
- ✅ Managementul redevențelor
- ✅ Mintare de NFT-uri noi
- ✅ Responsiv pe toate dispozitivele
- ✅ Pagina de debug pentru testare

---

## 2. Structura Proiectului

```
str-marketplace-dapp/
│
├── public/                          # Fișiere statice
│   ├── index.html                  # HTML de intrare
│   ├── manifest.json               # PWA manifest
│   └── robots.txt                  # SEO robots
│
├── src/                            # Codul sursă principal
│   │
│   ├── App.tsx                     # Componenta principală
│   ├── App.css                     # Stiluri principale
│   ├── index.tsx                   # Punct de intrare React
│   ├── index.css                   # Stiluri globale
│   │
│   ├── Components/                 # Componente reutilizabile
│   │   ├── Header.tsx/css          # Navigare și meniu
│   │   ├── Hero.tsx/css            # Secțiune de hero
│   │   ├── WalletButton.tsx/css    # Buton conexiune portofeu
│   │   ├── NetworkChecker.tsx/css  # Verificare rețea
│   │   ├── NFTMetadata.tsx/css     # Afișare metadate NFT
│   │   ├── Toast.tsx/css           # Notificări toast
│   │   ├── Pagination.tsx/css      # Paginare
│   │   ├── ConfirmationModal.tsx/css # Modal de confirmare
│   │   └── ErrorDisplay.tsx        # Afișare erori
│   │
│   ├── Pages/                      # Pagini principale
│   │   ├── Home.tsx/css            # Pagina de acasă
│   │   ├── Marketplace.tsx/css     # Marketplace NFT
│   │   ├── MyDomains.tsx/css       # Domeniile mele
│   │   ├── MyListings.tsx/css      # Listele mele de vânzare
│   │   ├── Royalties.tsx/css       # Gestionare redevențe
│   │   ├── Mint.tsx/css            # Mintare NFT
│   │   └── Debug.tsx/css           # Pagina de debug
│   │
│   ├── contexts/                   # React Context (gestionare stare globală)
│   │   ├── WalletContext.tsx       # Context portofeu
│   │   └── ToastContext.tsx        # Context notificări
│   │
│   ├── hooks/                      # Custom React Hooks
│   │   ├── useMarketplaceSDK.ts    # Hook SDK Marketplace
│   │   ├── useNetworkGuard.ts      # Hook verificare rețea
│   │   └── useNFTMetadata.ts       # Hook metadate NFT
│   │
│   ├── config/                     # Configurare aplicație
│   │   ├── constants.ts            # Constante și adrese contract
│   │   └── network.ts              # Configurare rețea blockchain
│   │
│   ├── contracts/                  # Fișiere contract
│   │   ├── README.md               # Documentare contracte
│   │   └── abis/                   # ABI-uri contracte
│   │       ├── Marketplace.json
│   │       ├── StrDomainsNFT.json
│   │       └── RoyaltySplitter.json
│   │
│   ├── sdk/                        # Software Development Kit
│   │   └── MarketplaceSDK.ts       # SDK complet Marketplace
│   │
│   ├── services/                   # Servicii backend
│   │   └── SubgraphService.ts      # Serviciu Subgraph GraphQL
│   │
│   ├── utils/                      # Funcții utilitare
│   │   ├── constants.ts            # Constante globale
│   │   ├── eventParser.ts          # Parser pentru evenimente blockchain
│   │   ├── helpers.ts              # Funcții ajutoare
│   │   ├── optimisticUpdates.ts    # Actualizări optimiste memorie
│   │   └── persistentOptimisticUpdates.ts # Actualizări persistent
│   │
│   ├── global.d.ts                 # Tipuri globale TypeScript
│   ├── react-app-env.d.ts          # Tipuri React
│   ├── setupTests.ts               # Setup teste
│   └── reportWebVitals.ts          # Raportare performanță
│
├── env.example                     # Exemplu variabile mediu
├── package.json                    # Dependențe și scripturi
├── tsconfig.json                   # Configurare TypeScript
├── README.md                        # Documentare generală
└── Documentatie.md                 # Această documentație
```

---

## 3. Tehnologii și Dependențe

### Dependențe Principale

| Pachet | Versiune | Utilizare |
|--------|----------|-----------|
| **react** | ^19.2.0 | Framework frontend |
| **react-dom** | ^19.2.0 | Rendering React în DOM |
| **typescript** | ^4.4.2 | Limbaj de programare |
| **ethers.js** | ^6.15.0 | Interacție blockchain Ethereum |
| **@walletconnect/ethereum-provider** | ^2.22.4 | Provider WalletConnect |
| **@walletconnect/modal** | ^2.7.0 | Modal pentru WalletConnect |
| **react-scripts** | 5.0.1 | Build tool și dev server |

### Dependențe de Testare

| Pachet | Versiune | Utilizare |
|--------|----------|-----------|
| **@testing-library/react** | ^16.1.0 | Testare componente React |
| **@testing-library/dom** | ^10.4.0 | Testare DOM |
| **@testing-library/jest-dom** | ^6.6.3 | Matcheri Jest |
| **@types/jest** | ^27.0.1 | Tipuri TypeScript pentru Jest |

### Dependențe de Dezvoltare

| Pachet | Versiune | Utilizare |
|--------|----------|-----------|
| **eslint** | ^8.57.1 | Linting cod |
| **prettier** | ^3.6.2 | Formatare cod |
| **eslint-config-prettier** | ^10.1.8 | Integrare Prettier cu ESLint |
| **eslint-plugin-react** | ^7.37.5 | Plugin ESLint pentru React |

### Versiuni Node

- **Node.js:** 16+ (recomandat 18+)
- **npm:** 8+ (inclus cu Node.js)

---

## 4. Configurare și Setup

### 4.1 Variabile de Mediu

Creați fișierul `.env.local` în rădăcina proiectului cu următoarele variabile:

```env
# Network Configuration
REACT_APP_CHAIN_ID=80002
REACT_APP_NETWORK_NAME=Polygon Amoy
REACT_APP_RPC_URL=https://rpc-amoy.polygon.technology
REACT_APP_BLOCK_EXPLORER_URL=https://amoy.polygonscan.com

# Contract Addresses
REACT_APP_MARKETPLACE_ADDRESS=0x75201083D96114982B1b08176C87E2ec3e39dDb1
REACT_APP_STR_DOMAIN_NFT_COLLECTION=0x8255d9f7f51AD2B5CC7fBDFc1D9A967bD19EDD6a
REACT_APP_SPLITTER_FACTORY_ADDRESS=0x4C50CEF9c518789CFd0E014d8b1582B2dFE19A3b
REACT_APP_SPLITTER_IMPLEMENTATION_ADDRESS=0xAAB70f560fee9Be4F03891c30B19A8EeE1eB2E80

# Treasury Addresses
REACT_APP_MARKETPLACE_TREASURY=0x3274874F7B5ad07A10bCa325d20878E4Ee8B9034
REACT_APP_NFT_ROYALTY_TREASURY=0x7b289068e2d3a87F71316AB3B85b13fF87975221

# Fee Configuration (în basis points: 100 bp = 1%)
REACT_APP_FEE_MARKETPLACE_BPS=250
REACT_APP_NFT_ROYALTY_TREASURY_BPS=500
REACT_APP_FEE_NFT_ROYALTY_BPS=500
REACT_APP_FEE_SPLITTER_CREATOR_BPS=500

# Subgraph URL
REACT_APP_SUBGRAPH_URL=https://api.studio.thegraph.com/query/YOUR_SUBGRAPH_ID

# Development
REACT_APP_DEVELOP=false
```

### 4.2 Instalarea Dependențelor

```bash
# Instalați dependențele cu npm
npm install
```

### 4.3 Pornirea Serverului de Dezvoltare

```bash
# Pornit mod dezvoltare cu hot reload
npm start

# În producție: construire optimizată
npm run build

# Servire producție local
npx serve -s build
```

### 4.4 Scripturi npm disponibile

```bash
npm start               # Pornit dev server (port 3000)
npm run build           # Construire producție
npm test                # Rulare teste
npm run eject           # Eject din create-react-app (ireversibil)
npm run clean           # Ștergere build și cache
npm run lint            # Verificare cod cu ESLint
npm run format          # Formatare cod cu Prettier
```

---

## 5. Arhitectura Aplicației

### 5.1 Flux de Date și State Management

```
┌─────────────────────────────────────┐
│   WalletContext (Stare Globală)     │
├─────────────────────────────────────┤
│ - account: string | null             │
│ - balance: string | null             │
│ - chainId: number | null             │
│ - provider: ethers.BrowserProvider   │
│ - signer: ethers.JsonRpcSigner       │
│ - connectWallet()                    │
│ - disconnectWallet()                 │
│ - switchNetwork()                    │
└─────────────────────────────────────┘
           ↓
┌─────────────────────────────────────┐
│   ToastContext (Notificări)          │
├─────────────────────────────────────┤
│ - showToast()                        │
│ - hideToast()                        │
└─────────────────────────────────────┘
           ↓
┌─────────────────────────────────────┐
│   Componente și Pagini               │
├─────────────────────────────────────┤
│ - Header, Hero, Components           │
│ - Pages (Home, Marketplace, etc.)    │
└─────────────────────────────────────┘
```

### 5.2 Fluxul Conexiei Portofelului

```
Utilizator clică WalletButton
        ↓
Selectează MetaMask sau WalletConnect
        ↓
Apelează connectWallet() din WalletContext
        ↓
Cere permisiune browser
        ↓
Primește provider și signer
        ↓
Setează contul, balanța, chainId
        ↓
Stochează preferință în localStorage
        ↓
Actualizează UI
```

### 5.3 Fluxul Tranzacțiilor

```
Utilizator inițiază acțiune (vânzare, cumpărare, etc.)
        ↓
Validare date de intrare
        ↓
Construire apel contract via MarketplaceSDK
        ↓
Cerere semnare tranzacție (portofeu)
        ↓
Transmitere blockchain
        ↓
Așteptare confirmări
        ↓
Actualizare locală optimistă
        ↓
Sincronizare cu Subgraph
        ↓
Actualizare UI finală
```

---

## 6. Componente Principale

### 6.1 Header.tsx

**Scop:** Navigare principală și informații portofeu

**Proprietăți:**
```typescript
interface HeaderProps {
  currentPage: string;
  onNavigate: (page: string) => void;
}
```

**Funcționalități:**
- Afișare logo și titlu
- Link-uri navigare (Marketplace, Domeniile Mele, etc.)
- Buton conexiune portofeu
- Afișare cont și rețea conectate
- Meniu mobil responsive

### 6.2 WalletButton.tsx

**Scop:** Conexiune/deconexiune portofeu

**Stări:**
- Deconectat - buton "Conectează Portofeu"
- Se conectează - indicator de încărcare
- Conectat - afișare adresă cont
- Eroare de rețea - buton "Schimbă Rețea"

### 6.3 NetworkChecker.tsx

**Scop:** Verificare și validare rețea blockchain

**Verificări:**
- Chain ID corect (80002 pentru Amoy)
- Provider disponibil
- RPC accessible
- Afișare avertisment dacă rețea greșită

### 6.4 Hero.tsx

**Scop:** Secțiune de intrare pe pagina de acasă

**Afișează:**
- Titlu și descriere
- Statistici colecție (NFT-uri, volume, etc.)
- Butoane CTA (Call To Action)
- Animații și gradient-uri

### 6.5 NFTMetadata.tsx

**Scop:** Afișare detalii NFT

**Informații:**
- ID token și nume
- Imagine și descriere
- Proprietar și valoare
- Istorie tranzacții
- Detecție și validare metadate

### 6.6 Pagination.tsx

**Scop:** Navigare prin liste mari de NFT-uri

**Proprietăți:**
```typescript
interface PaginationProps {
  currentPage: number;
  totalPages: number;
  onPageChange: (page: number) => void;
  itemsPerPage?: number;
}
```

### 6.7 ConfirmationModal.tsx

**Scop:** Confirmare acțiuni critice

**Utilizări:**
- Confirmare vânzare NFT
- Confirmare anulare listing
- Confirmare retragere redevențe

### 6.8 Toast.tsx

**Scop:** Notificări utilizator non-intruzive

**Tipuri:**
- success (verde)
- error (roșu)
- warning (galben)
- info (albastru)

---

## 7. Contextul și Gestionarea Stării

### 7.1 WalletContext.tsx

**Rol:** Gestionare globală a stării portofelului și conexiunilor blockchain

**Stare:**
```typescript
interface WalletContextType {
  account: string | null;                    // Adresa cont
  balance: string | null;                    // Balanță MATIC
  chainId: number | null;                    // ID rețea
  provider: ethers.BrowserProvider | null;   // Provider Ethereum
  signer: ethers.JsonRpcSigner | null;       // Signer pentru tranzacții
  connectWallet: (walletType?: WalletType) => Promise<void>;
  disconnectWallet: () => void;
  switchNetwork: (chainId: number) => Promise<void>;
  isConnecting: boolean;
  isNetworkSwitching: boolean;
  error: string | null;
  setError: (error: string | null) => void;
  walletType: WalletType | null;             // "metamask" | "walletconnect"
}
```

**Funcții Principale:**

#### connectWallet(walletType?)
```typescript
// Conectare MetaMask (implicit)
await connectWallet("metamask");

// Conectare WalletConnect
await connectWallet("walletconnect");

// Cere permisiuni, setează provider/signer
```

#### disconnectWallet()
```typescript
// Deconectare
disconnectWallet();

// Șterge localStorage
// Resetează stare
```

#### switchNetwork(chainId)
```typescript
// Schimbare rețea
await switchNetwork(80002);

// Suportă schimbare automată MetaMask
```

### 7.2 ToastContext.tsx

**Rol:** Gestionare notificări toast globale

**Stare și Funcții:**
```typescript
interface Toast {
  id: string;
  message: string;
  type: "success" | "error" | "warning" | "info";
  duration?: number; // millisecunde
}

// Utilizare:
showToast("Tranzacție reușită!", "success", 3000);
hideToast(toastId);
```

---

## 8. Servicii și SDK

### 8.1 MarketplaceSDK.ts

**Scop:** Interfață completă cu contractele Marketplace

**Clase și Metode Principale:**

#### Constructor
```typescript
constructor(
  signer: ethers.Signer,
  marketplaceAddress: string,
  nftAddress: string,
  subgraphUrl: string,
  develop?: boolean
)
```

#### Metode de Lectură

##### getActiveListings()
```typescript
// Obține toate listele active din Subgraph
const listings = await sdk.getActiveListings();
// Returns: ListedToken[]
```

##### getListingsForSeller(sellerAddress)
```typescript
// Obține listele pentru un vânzător specific
const myListings = await sdk.getListingsForSeller(account);
```

##### getListingForToken(tokenId, nftAddress)
```typescript
// Obține detalii listing pentru token specific
const listing = await sdk.getListingForToken(tokenId, nftAddress);
```

##### getTokensByOwner(ownerAddress)
```typescript
// Obține toate NFT-urile deținute de proprietar
const myDomains = await sdk.getTokensByOwner(account);
```

##### getBalance(address)
```typescript
// Obține balanța MATIC a unei adrese
const balance = await sdk.getBalance(address);
// Returns: string (în unități mici - wei)
```

##### getSplitterBalance(splitterAddress)
```typescript
// Obține balanța redevenței unei splitter
const balance = await sdk.getSplitterBalance(splitterAddress);
```

#### Metode de Scriere (Tranzacții)

##### listToken(nftAddress, tokenId, price)
```typescript
// Listează NFT pentru vânzare
const tx = await sdk.listToken(
  "0x8255d9f7f51AD2B5CC7fBDFc1D9A967bD19EDD6a",
  "1",
  ethers.parseUnits("100", 18) // 100 MATIC
);

// Așteptă confirmări
const receipt = await tx.wait(1);
```

##### buyToken(nftAddress, tokenId, price)
```typescript
// Cumpără NFT listat
const tx = await sdk.buyToken(
  "0x8255d9f7f51AD2B5CC7fBDFc1D9A967bD19EDD6a",
  "1",
  ethers.parseUnits("100", 18)
);
```

##### updateListing(nftAddress, tokenId, newPrice)
```typescript
// Actualizează prețul listing-ului
const tx = await sdk.updateListing(
  nftAddress,
  tokenId,
  newPrice
);
```

##### cancelListing(nftAddress, tokenId)
```typescript
// Anulează listing-ul
const tx = await sdk.cancelListing(nftAddress, tokenId);
```

##### withdrawFromSplitter(splitterAddress)
```typescript
// Retrage redevențe din splitter
const tx = await sdk.withdrawFromSplitter(splitterAddress);
```

#### Metode Event Parsing

##### getAllEventsForAddress(address, eventType?)
```typescript
// Obține toate evenimentele pentru o adresă
const events = await sdk.getAllEventsForAddress(account);
```

### 8.2 SubgraphService.ts

**Scop:** Interogări GraphQL către The Graph

**Interfețe Principale:**

```typescript
interface FormattedToken {
  id: string;
  tokenId: string;
  owner: string;
  uri: string;
  metadata?: {
    name: string;
    description: string;
    image: string;
  };
}

interface ListedToken extends FormattedToken {
  listing: {
    id: string;
    seller: string;
    price: string;
    active: boolean;
    timestamp: number;
  };
}
```

**Metode:**

#### getActiveListings()
```typescript
// Query pentru liste active
const query = gql`
  query {
    listings(where: { active: true }) {
      seller
      nft
      tokenId
      price
      active
    }
  }
`;
```

#### getTokensByOwner(ownerAddress)
```typescript
// Obține NFT-uri după proprietar
```

#### getPastEvents(eventName, filters)
```typescript
// Obține evenimente istorice filtrând după parametri
```

---

## 9. Contracte Inteligente

### 9.1 Marketplace Contract

**Adresă:** 0x75201083D96114982B1b08176C87E2ec3e39dDb1

**Funcții Principale:**

#### list(nft, tokenId, price)
```solidity
// Listează NFT pentru vânzare
marketplace.list(nftAddress, tokenId, price);
```

#### buy(nft, tokenId)
```solidity
// Cumpără NFT listat
marketplace.buy{value: price}(nftAddress, tokenId);
```

#### updateListing(nft, tokenId, newPrice)
```solidity
// Actualizează prețul
marketplace.updateListing(nftAddress, tokenId, newPrice);
```

#### cancelListing(nft, tokenId)
```solidity
// Anulează listing
marketplace.cancelListing(nftAddress, tokenId);
```

#### withdrawFees()
```solidity
// Retrage taxe marketplace pentru trezorerie
marketplace.withdrawFees();
```

**Evenimente:**

- `Listed(seller, nft, tokenId, price, timestamp)`
- `Purchased(buyer, seller, nft, tokenId, price, timestamp)`
- `ListingCanceled(seller, nft, tokenId, timestamp)`
- `ListingUpdated(seller, nft, tokenId, newPrice, timestamp)`
- `FeeWithdrawn(amount, timestamp)`

### 9.2 StrDomainsNFT Contract

**Adresă:** 0x8255d9f7f51AD2B5CC7fBDFc1D9A967bD19EDD6a

**Funcții Principale:**

#### mint(to, uri)
```solidity
// Mintează NFT nou
nft.mint(toAddress, metadataURI);
```

#### ownerOf(tokenId)
```solidity
// Obține proprietar token
```

#### balanceOf(owner)
```solidity
// Obține număr de NFT-uri deținute
```

#### approve(to, tokenId)
```solidity
// Aprobă contract Marketplace să mânuiască NFT
nft.approve(marketplaceAddress, tokenId);
```

**Evenimente:**

- `Transfer(from, to, tokenId)`
- `Approval(owner, approved, tokenId)`
- `Minted(to, tokenId, uri)`

### 9.3 RoyaltySplitter Contract

**Adresă:** 0xAAB70f560fee9Be4F03891c30B19A8EeE1eB2E80

**Scop:** Distribuție automată redevențe

**Funcții:**

#### withdrawAll()
```solidity
// Retrage și distribuie redevențe tuturor beneficiarilor
splitter.withdrawAll();
```

#### withdraw(address payable account)
```solidity
// Retrage pentru beneficiar specific
splitter.withdraw(address);
```

#### getBalance()
```solidity
// Obține balanța curentă
```

---

## 10. Funcționalități Detaliate

### 10.1 Pagina Marketplace

**Funcții:**
- Afișare listă pagină cu NFT-uri active
- Filtrare după preț, proprietar, etc.
- Search NFT-uri după nume/ID
- Paginare prin rezultate
- Cumpărare directă NFT-uri
- Vizualizare detalii și istoric

**Flux de cumpărare:**
```
1. Utilizator conectează portofeu
2. Navighează Marketplace
3. Selectează NFT
4. Clică "Cumpărare"
5. Confirmă în portofeu
6. Așteptă confirmări blockchain
7. Domeniu apare în "Domeniile Mele"
```

### 10.2 Pagina Domeniile Mele

**Funcții:**
- Afișare toate NFT-urile deținute
- Filtrare după status (listat/nelistat)
- Listare pentru vânzare
- Anulare listing
- Vizualizare detalii token

### 10.3 Pagina Listele Mele

**Funcții:**
- Afișare listing-uri proprii active
- Actualizare preț
- Anulare listing
- Vizualizare istoric oferte
- Monitorizare vânzări

### 10.4 Pagina Redevențe

**Funcții:**
- Vizualizare balanță redevențe
- Retragere redevențe
- Istoric distribuție
- Detalii beneficiari

### 10.5 Pagina Mintare

**Funcții:**
- Inputuri pentru URI metadate
- Validare URI
- Mintare NFT
- Confirmare tranzacție
- Afișare token nou minat

### 10.6 Pagina Debug

**Funcții:**
- Vizualizare configurare rețea
- Test conexiuni RPC
- Inspecție state global
- Detalii contract
- Jurnal evenimente

---

## 11. Ghid de Dezvoltare

### 11.1 Adăugare Componență Nouă

**1. Creare fișier componentă:**
```typescript
// src/Components/MyNewComponent.tsx
import React from "react";
import "./MyNewComponent.css";

interface MyNewComponentProps {
  title: string;
  onAction?: () => void;
}

const MyNewComponent: React.FC<MyNewComponentProps> = ({ 
  title, 
  onAction 
}) => {
  return (
    <div className="my-new-component">
      <h2>{title}</h2>
      <button onClick={onAction}>Action</button>
    </div>
  );
};

export default MyNewComponent;
```

**2. Creare stiluri:**
```css
/* src/Components/MyNewComponent.css */
.my-new-component {
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 8px;
}

.my-new-component h2 {
  margin-top: 0;
  color: #333;
}

.my-new-component button {
  background-color: #007bff;
  color: white;
  padding: 8px 16px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.my-new-component button:hover {
  background-color: #0056b3;
}
```

**3. Utilizare în pagină:**
```typescript
import MyNewComponent from "../Components/MyNewComponent";

function MyPage() {
  return (
    <div>
      <MyNewComponent 
        title="Test"
        onAction={() => console.log("Action!")}
      />
    </div>
  );
}
```

### 11.2 Utilizare Hook Custom

**Exemplu: useMarketplaceSDK**
```typescript
import { useMarketplaceSDK } from "../hooks/useMarketplaceSDK";

function MyComponent() {
  const { sdk, loading, error } = useMarketplaceSDK();

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  const handleBuy = async (tokenId: string) => {
    const tx = await sdk?.buyToken(
      NFT_ADDRESS,
      tokenId,
      price
    );
    await tx?.wait();
  };

  return <button onClick={() => handleBuy("1")}>Buy</button>;
}
```

### 11.3 Apelarea Contextului

**Exemplu: useWallet**
```typescript
import { useWallet } from "../contexts/WalletContext";

function MyComponent() {
  const { 
    account, 
    balance, 
    connectWallet, 
    error 
  } = useWallet();

  if (!account) {
    return <button onClick={() => connectWallet()}>
      Connect
    </button>;
  }

  return (
    <div>
      <p>Account: {account}</p>
      <p>Balance: {balance} MATIC</p>
    </div>
  );
}
```

### 11.4 Adăugare Pagină Nouă

**1. Creare pagina:**
```typescript
// src/Pages/NewPage.tsx
import React from "react";
import "./NewPage.css";

interface NewPageProps {
  onNavigate?: (page: string) => void;
}

const NewPage: React.FC<NewPageProps> = ({ onNavigate }) => {
  return (
    <div className="new-page">
      <h1>New Page</h1>
      <p>Content here</p>
    </div>
  );
};

export default NewPage;
```

**2. Registrare în App.tsx:**
```typescript
import NewPage from "./Pages/NewPage";

// În funcția renderPage():
case "new-page":
  return <NewPage />;
```

**3. Adăugare link navigare:**
```typescript
// În Header.tsx
<button onClick={() => onNavigate("new-page")}>
  New Page
</button>
```

### 11.5 Formatare și Linting

```bash
# Verificare cod
npm run lint

# Formatare automat
npm run format

# Repair probleme ESLint
npm run lint -- --fix
```

---

## 12. Mentenanță și Troubleshooting

### 12.1 Probleme Comune

#### 1. "Portofeu nu se conectează"

**Cauze:**
- MetaMask nu este instalat
- WalletConnect URL incorect
- Versiune veche de browser

**Soluții:**
```typescript
// Verificare MetaMask disponibilitate
if (typeof window.ethereum === 'undefined') {
  console.log('Please install MetaMask');
}

// Verificare conectare
const accounts = await window.ethereum.request({
  method: 'eth_requestAccounts'
});
```

#### 2. "Rețea greșită"

**Verificare rețea:**
```typescript
const chainId = await window.ethereum.request({
  method: 'eth_chainId'
});
console.log(`Current chain: ${parseInt(chainId)}`);

// Schimbă la Amoy
await window.ethereum.request({
  method: 'wallet_switchEthereumChain',
  params: [{ chainId: '0x13882' }], // 80002 în hex
});
```

#### 3. "Tranzacție eșuată"

**Verificare balanță:**
```typescript
const balance = await provider.getBalance(account);
console.log(`Balance: ${ethers.formatUnits(balance, 18)} MATIC`);

// Dacă 0, obține MATIC din faucet
// https://faucet.polygon.technology
```

#### 4. "Subgraph inexact"

**Soluții:**
- Verifică URL Subgraph în .env
- Așteptă indexare date
- Verifica loggurile Subgraph Studio

### 12.2 Debug și Logging

```typescript
// Activare debug mode în .env
REACT_APP_DEVELOP=true

// Logging SDK
const sdk = new MarketplaceSDK(...);
// Aceasta va loga în consolă

// Verificare state în Redux DevTools (dacă instalat)
// Utilizează componenta Debug din pagina /debug
```

### 12.3 Testare

```bash
# Rulare teste unitare
npm test

# Modo watch
npm test -- --watch

# Coverage report
npm test -- --coverage
```

### 12.4 Build și Deployment

```bash
# Build producție optimizat
npm run build

# Rulare local producție
npx serve -s build
```

---

## 13. Securitate și Best Practices

### 13.1 Securitate Portofeu

```typescript
// ❌ INCORECT - nu stoca private keys
const privateKey = "0x...";

// ✅ CORECT - utilizează provider sigur
const signer = await provider.getSigner();

// ✅ CORECT - cere semnare prin portofeu
const tx = await signer.signTransaction(txData);
```

### 13.2 Validare Date

```typescript
// Validare adresa Ethereum
if (!ethers.isAddress(address)) {
  throw new Error("Invalid Ethereum address");
}

// Validare amount
const amount = ethers.parseUnits(input, 18);
if (amount <= 0n) {
  throw new Error("Invalid amount");
}

// Validare URL
try {
  new URL(uri);
} catch {
  throw new Error("Invalid URL");
}
```

### 13.3 Gestionare Erori

```typescript
try {
  const tx = await sdk.buyToken(nft, tokenId, price);
  await tx.wait(1);
  showToast("Purchase successful!", "success");
} catch (error: any) {
  if (error.code === 'ACTION_REJECTED') {
    showToast("Transaction rejected", "warning");
  } else if (error.code === 'INSUFFICIENT_FUNDS') {
    showToast("Insufficient funds", "error");
  } else {
    showToast(`Error: ${error.message}`, "error");
  }
}
```

### 13.4 Best Practices

#### 1. Utilizare Constante
```typescript
// src/utils/constants.ts
export const AMOY_CHAIN_ID = 80002;
export const GAS_BUFFER = 1.15; // +15% buffer
export const CONFIRMATION_BLOCKS = 1;
```

#### 2. Gestionare State Corect
```typescript
// Utilizare const și immutability
const [state, setState] = useState<StateType>(initialState);

// Update corect
setState(prevState => ({
  ...prevState,
  field: newValue
}));
```

#### 3. Cleanup și Memory Leaks
```typescript
useEffect(() => {
  const handler = () => {
    // Handle event
  };

  window.addEventListener('resize', handler);

  // Cleanup
  return () => {
    window.removeEventListener('resize', handler);
  };
}, []);
```

#### 4. Optimizare Performance
```typescript
// Utilizare React.memo pentru componente pure
const MyComponent = React.memo(({ data }) => {
  return <div>{data}</div>;
});

// Utilizare useMemo pentru calcule grele
const expensiveValue = useMemo(() => {
  return computeExpensiveValue(data);
}, [data]);

// Utilizare useCallback pentru funcții stabile
const handleClick = useCallback(() => {
  console.log("Clicked");
}, []);
```

### 13.5 Verificare Siguranță Contracte

Înainte de utilizare în producție:
- [ ] Auditare de cod (smart contract security audit)
- [ ] Testare exhaustivă
- [ ] Verificare pericole cunoscute
- [ ] Documentare comportament
- [ ] Testare front-end cu contract verificat

---

## Concluzii

Acest proiect oferă o interfață completă și modernă pentru interacțiune cu marketplace-ul NFT al domeniilor STR. Arhitectura modulară și bine organizată permite ușor extindere și mentenanță.

### Pentru Noi Dezvoltatori:
1. Studiați structura folderelor și fișierelor
2. Porniți serverul dev cu `yarn start`
3. Inspectați pagina Debug pentru stare aplicație
4. Utilizați WalletContext pentru stare portofeu
5. Utilizați MarketplaceSDK pentru interacțiuni blockchain

### Pentru Mainteners:
1. Actualizați .env cu noi adrese contract (dacă necesare)
2. Rulați linting și testing regular
3. Monitorizați erori în producție
4. Documentați schimbări majore
5. Testați pe testnet înainte de producție

### Următorii Pași:
- [ ] Implementare teme (dark mode)
- [ ] Suport multiple rețele
- [ ] Caching mai avansat
- [ ] Optimizare imagini
- [ ] Implementare PWA (offline)
- [ ] Analytics și monitoring
- [ ] Suport pentru mai mult portofeluri

---

## Contact și Suport

Pentru întrebări și probleme:
- Consultați pagina Debug din aplicație
- Verificați console browser (F12 → Console)
- Inspectați rețeaua (F12 → Network)
- Consultați logs blockchain (Block Explorer)

## Contact
Pentru întrebări suplimentare: 3JlblgEHb88@gmail.com