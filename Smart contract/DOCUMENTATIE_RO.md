# Documentație Tehnică - Proiect StrDomains Smart Contracts

## Cuprins

1. [Descriere Generală a Proiectului](#descriere-generală)
2. [Arhitectura Sistemului](#arhitectura-sistemului)
3. [Componente Principale](#componente-principale)
4. [Contracte Inteligente Detaliate](#contracte-inteligente-detaliate)
5. [Fluxuri de Tranzacții](#fluxuri-de-tranzacții)
6. [Instalare și Configurare](#instalare-și-configurare)
7. [Deployment](#deployment)
8. [Integrare și Utilizare](#integrare-și-utilizare)
9. [Securitate și Considerații](#securitate-și-considerații)
10. [FAQ și Troubleshooting](#faq-și-troubleshooting)

---

## Descriere Generală

### Ce este StrDomains?

StrDomains este un ecosistem blockchain construit pe **Solidity 0.8.24** pentru managementul domeniilor NFT (non-fungible tokens) cu suport complet pentru **redevențe (royalties)** și o **piață descentralizată (marketplace)** pentru vânzarea și cumpărarea domeniilor.

### Obiectivele Principale

- **Creație și Management NFT**: Permite crearea și gestionarea unor domenii unice ca NFT-uri pe blockchain
- **Sistem de Redevențe**: Suportă distribuția automată a redevențelor către creator și trezorerie
- **Piață Descentralizată**: Permite vânzarea și cumpărarea domeniilor NFT cu suport pentru monede native și ERC-20
- **Flexibilitate**: Suportă plăți în ETH și alte tokenuri ERC-20

### Tehnologii Utilizate

```
- Ethereum Smart Contracts (Solidity ^0.8.24)
- OpenZeppelin Contracts (v5.4.0) - pentru ERC721, ERC20, Access Control, etc.
- Hardhat - Framework pentru desenvolvimento și testing
- TypeScript - pentru scripturi de deployment și testing
- Ethers.js v6 - Biblioteca pentru interacțiune cu blockchain
```

---

## Arhitectura Sistemului

```
┌─────────────────────────────────────────────────────────────┐
│                    Utilizatori Finali                        │
└────┬──────────────────────────────────────────────────────┬──┘
     │                                                      │
     ▼                                                      ▼
┌──────────────────┐                        ┌────────────────────────┐
│ Creatori/Vânzători│                        │ Cumpărători/Investitori│
└────────┬─────────┘                        └──────────┬─────────────┘
         │                                             │
         │  mint()/list()                              │  buy()
         │                                             │
         ▼─────────────────────────────────────────────▼
    ┌─────────────────────────────────────────────────────────┐
    │              MARKETPLACE CONTRACT                        │
    │  - Listare domenii NFT                                  │
    │  - Vânzare cu ETH și ERC-20                             │
    │  - Colectare comisioane                                 │
    └────────┬──────────────────────────────────┬─────────────┘
             │                                  │
             │  recordSale()                    │  royaltyInfo()
             │                                  │
             ▼                                  ▼
    ┌──────────────────────────────────────────────────────────┐
    │            STR_DOMAINS_NFT CONTRACT                       │
    │  - Colecția ERC721 de domenii                            │
    │  - Management redevențe (EIP-2981)                       │
    │  - Mapare bijecție: domeniu ↔ tokenId                    │
    │  - Istoric vânzări per token                             │
    └────────┬──────────────────────────────┬──────────────────┘
             │                              │
             │  createSplitter()            │  getTokenData()
             │                              │
             ▼                              ▼
    ┌──────────────────────────────────────────────────────────┐
    │        ROYALTY_SPLITTER_FACTORY                           │
    │  - Creează clone EIP-1167 pentru fiecare NFT             │
    │  - Inițializează cu split-uri creator/treasury           │
    └────────┬───────────────────────────────────────────────┬─┘
             │                                               │
             │  clone() → init()                             │
             │                                               │
             ▼                                               ▼
    ┌──────────────────────────────────────────────────────────┐
    │        ROYALTY_SPLITTER (per Token)                      │
    │  - Colectează redevențe în ETH și ERC-20                │
    │  - Distribuie creator (40%) și treasury (60%)           │
    │  - Permite withdraw pentru creator și treasury           │
    └──────────────────────────────────────────────────────────┘
```

---

## Componente Principale

### 1. **Colecția NFT** (StrDomainsNFT.sol)
Contractul ERC721 care reprezintă domenii ca NFT-uri cu redevențe integrate.

### 2. **Piața (Marketplace)** (Marketplace.sol)
Permite tranzacționarea domeniilor NFT cu suport pentru monede native și ERC-20.

### 3. **Divisor de Redevențe** (RoyaltySplitter.sol)
Contract care colectează și distribuie redevențe între creator și trezorerie.

### 4. **Fabrica de Divisori** (RoyaltySplitterFactory.sol)
Creează instanțe minimaliste de RoyaltySplitter pentru fiecare NFT (usando EIP-1167 clones).

---

## Contracte Inteligente Detaliate

### StrDomainsNFT.sol

**Scopul**: Contract ERC721 principal care gestionează colecția de domenii NFT cu suport pentru redevențe și istoric de vânzări.

#### Variabile de Stare Principale

```solidity
// Roluri
bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
bytes32 public constant SALES_ROLE = keccak256("SALES_ROLE");

// Configurare redevențe
uint96 public constant DEFAULT_ROYALTY_BPS = 500;      // 5% din preț
uint16 public constant CREATOR_SHARE_IN_ROY = 4000;    // 40% din redevență (= 2% din preț)
uint16 public constant TREASURY_SHARE_IN_ROY = 6000;   // 60% din redevență (= 3% din preț)

// Mappings
mapping(uint256 => address) private _creator;              // cine a creat NFT-ul
mapping(uint256 => uint64) private _mintedAt;             // timp de creare
mapping(uint256 => uint256) private _lastSalePrice;       // preț ultima vânzare
mapping(uint256 => uint64) private _lastSaleAt;           // timp ultima vânzare
mapping(string => uint256) private _domainToTokenId;      // domeniu → ID token
mapping(uint256 => string) private _tokenIdToDomain;      // ID token → domeniu
```

#### Constructor

```solidity
constructor(
    string memory name_,
    string memory symbol_,
    address treasury_,
    address splitterFactory_,
    uint96 /*ignored*/
)
```

**Parametri:**
- `name_`: Denumirea colecției ("Str Domains")
- `symbol_`: Simbolul colecției ("STRDOM")
- `treasury_`: Adresa trezorieriei pentru colectarea redevențelor
- `splitterFactory_`: Adresa fabricii pentru crearea divisorilor
- Ultimul parametru este ignorat (pentru compatibilitate)

**Evenimente emise:**
- `DefaultRoyaltyUpdated`
- `SplitterFactoryUpdated`

#### Funcțiile Principale

##### 1. `mint(address to, string memory uri, string memory domainName)`

Creează un NFT nou și îl atribuie unui adres.

```solidity
function mint(address to, string memory uri, string memory domainName)
    external
    onlyRole(MINTER_ROLE)
    returns (uint256 tokenId)
```

**Parametri:**
- `to`: Adresa care va primi NFT-ul
- `uri`: Metadate JSON (conform ERC721 standard)
- `domainName`: Numele domeniului (unic, ex: "vip.str")

**Valori returnate:**
- `tokenId`: ID-ul noului token (incrementat automat)

**Logică:**
1. Validează că adresa nu este nulă, domeniul nu este gol și domeniul nu există deja
2. Creează NFT-ul cu `_safeMint()`
3. Setează metadatele URI
4. Stochează informațiile creatorului și timpului de creare
5. Creează mappinguri bidireccionale domeniu ↔ tokenId
6. Apelează fabrica pentru a crea un divisor de redevențe special pentru acest token
7. Setează redevențele per token folosind EIP-2981
8. Emite evenimentul `Minted`

**Evenimente emise:**
- `Minted(tokenId, to, to, uri, domainName)`
- `TokenSplitterSet(tokenId, splitter, 500)` // 5%

##### 2. `recordSale(uint256 tokenId, uint256 price, address buyer)`

Înregistrează o vânzare pe blockchain (apelat de piață).

```solidity
function recordSale(uint256 tokenId, uint256 price, address buyer)
    external
    onlyRole(SALES_ROLE)
```

**Parametri:**
- `tokenId`: ID-ul domeniului vândut
- `price`: Preț de vânzare
- `buyer`: Adresa cumpărătorului

**Efect:** Actualizează `_lastSalePrice` și `_lastSaleAt` pentru istoric.

##### 3. `settleSaleERC20(...)`

Funcție internă avansată pentru decontare vânzări cu tokenuri ERC-20. Gestionează:
- Transfer fonduri de la cumpărător
- Calculare și transfer redevențe către splitter
- Transfer rest vânzătorului
- Transfer NFT-ului la cumpărător

##### 4. `setTreasury(address newTreasury)`

Schimbă adresa trezorieriei.

```solidity
function setTreasury(address newTreasury)
    external
    onlyRole(DEFAULT_ADMIN_ROLE)
```

**Efect:** Actualizează treasury și redevențele implicite.

##### 5. `burn(uint256 tokenId)`

Distruge un NFT și curață mappingurile de domeniu.

```solidity
function burn(uint256 tokenId)
    public
    override
    onlyRole(DEFAULT_ADMIN_ROLE)
```

#### Funcții de Citire (View)

- **`getLastId()`**: Returnează ultimul ID de token mint-at
- **`getTokenData(uint256 tokenId)`**: Informații complete despre un token
- **`getTokenDataByDomain(string domainName)`**: Informații după domeniu
- **`creatorOf(uint256 tokenId)`**: Cine a creat token-ul
- **`mintedAt(uint256 tokenId)`**: Data creării
- **`lastSaleOf(uint256 tokenId)`**: Preț și dată ultima vânzare

---

### Marketplace.sol

**Scopul**: Piață descentralizată pentru vânzarea și cumpărarea domeniilor NFT cu suport pentru ETH și ERC-20.

#### Structuri Principale

```solidity
struct Listing {
    address seller;
    address nft;
    uint256 tokenId;
    uint256 price;
    address paymentToken;  // address(0) = native token (ETH)
    bool active;
}
```

#### Variabile de Stare

```solidity
uint96 public marketplaceFeeBps;           // comision piață (ex: 250 = 2.5%)
address public feeTreasury;                // unde se strâng comisioane
uint256 public accruedFees;                // comisioane acumulate în ETH
mapping(address => uint256) accruedTokenFees;  // comisioane pe token ERC-20
mapping(uint256 => Listing) public listings;   // listările
```

#### Funcții Principale

##### 1. `listToken(address nft, uint256 tokenId, uint256 price)`

Listează un domeniu NFT pentru vânzare cu ETH.

```solidity
function listToken(address nft, uint256 tokenId, uint256 price)
    external
    returns (uint256 listingId)
```

**Logică:**
1. Validează că expeditorul este proprietar și NFT-ul este aprobat
2. Transferă NFT-ul în escrow la piață
3. Creează listarea cu `price` și `paymentToken = address(0)`
4. Returnează ID-ul listării

##### 2. `listTokenERC20(...)`

Listează un domeniu NFT pentru vânzare cu token ERC-20.

```solidity
function listTokenERC20(address nft, uint256 tokenId, uint256 price, address paymentToken)
    external
    returns (uint256 listingId)
```

**Diferență:** Setează `paymentToken` la adresa tokenului și preț în unitățile acelui token.

##### 3. `updateListing(uint256 listingId, uint256 newPrice)`

Vânzătorul poate actualiza preț pentru o listare activă.

##### 4. `cancelListing(uint256 listingId)`

Anulează o listare și returnează NFT-ul la vânzător.

```solidity
function cancelListing(uint256 listingId)
    external
    nonReentrant
```

**Logică:**
1. Validează că listarea este activă și expeditorul este vânzător
2. Marcheaza listarea ca inactivă
3. Transferă NFT-ul din escrow înapoi la vânzător

##### 5. `buy(uint256 listingId)`

Cumpăritor plătește în ETH și primește NFT-ul.

```solidity
function buy(uint256 listingId)
    external
    payable
    nonReentrant
```

**Flux de plăți:**
1. Veririfică că `msg.value == price`
2. Calculează redevențe (5% din preț)
3. Calculează comision piață (ex: 2.5%)
4. `sellerAmount = price - royalties - fee`
5. Trimite redevențe → splitter
6. Trimite comision → `accruedFees`
7. Trimite rest vânzătorului
8. Transferă NFT-ul la cumpărător
9. Apelează `recordSale()` pe colecție

**Exemplu (preț 100 ETH):**
```
Preț total:           100 ETH
Redevențe (5%):        5 ETH → Splitter
Comision piață (2.5%): 2.5 ETH → feeTreasury
Vânzător primește:    92.5 ETH
```

##### 6. `buyWithERC20(uint256 listingId)`

Identic cu `buy()` dar pentru tokenuri ERC-20. Comisioanele se acumulează în `accruedTokenFees`.

##### 7. `withdrawFees()` și `withdrawTokenFees(address token)`

Admin-ul piață retrage comisioanele acumulate.

```solidity
function withdrawFees()
    external
    onlyRole(ADMIN_ROLE)
    nonReentrant
```

#### Funcții Admin

- **`setMarketplaceFeeBps(uint96 feeBps)`**: Setează comision (max 20%)
- **`setFeeTreasury(address t)`**: Schimbă trezoreria comisioanelor

---

### RoyaltySplitter.sol

**Scopul**: Contract care colectează redevențe și le distribuie automat între creator și trezorerie.

**Mecanismul de distribuție**: Când contractul primește fonduri, le împarte imediat:
- 40% → Creator
- 60% → Treasury

#### Variabile de Stare

```solidity
address public creator;
address public treasury;
uint16 public creatorBps;      // 4000 (40%)
uint16 public treasuryBps;     // 6000 (60%)

mapping(address => uint256) public ethBalance;
mapping(address => mapping(address => uint256)) public erc20Balance;
```

#### Funcții Principale

##### 1. `init(address _creator, address _treasury, uint16 _creatorBps, uint16 _treasuryBps)`

Inițializează splitter-ul (se apelează din fabrică).

```solidity
function init(address _creator, address _treasury, uint16 _creatorBps, uint16 _treasuryBps)
    external
    onlyOnce
```

**Constrângeri:**
- `_creatorBps + _treasuryBps == 10000` (100%)
- Se poate apela doar o dată

##### 2. `depositToken(address token, uint256 amount)`

Primește token-uri ERC-20, le împarte și le depozitează în mappinguri.

```solidity
function depositToken(address token, uint256 amount)
    external
    nonReentrant
```

**Logică:**
1. Transferă tokenuri de la apelant
2. Calculează: `toCreator = amount * creatorBps / 10000`
3. Calculează: `toTreasury = amount - toCreator`
4. Actualizează balansurile în mapping-uri

##### 3. `receive()` și `fallback()`

Permite contractului să primească ETH direct. Când primește, le distribuie imediat.

```solidity
receive() external payable {
    _collectNative(msg.value);
}
```

**Logică interna (_collectNative):**
```
ethBalance[creator] += msg.value * 40%
ethBalance[treasury] += msg.value * 60%
```

##### 4. `withdraw()`

Creator sau treasury retrage balanța lor de ETH.

```solidity
function withdraw()
    external
    nonReentrant
```

**Logică:**
1. Citește balanța `msg.sender`
2. Setează balanța la 0
3. Trimite ETH-ul

##### 5. `withdrawToken(address token)`

Retrage balanța de token ERC-20.

```solidity
function withdrawToken(address token)
    external
    nonReentrant
```

##### 6. `updateCreator(address newCreator)` și `updateTreasury(address newTreasury)`

Permit schimbarea adreselor și transferul balansurilor curente.

---

### RoyaltySplitterFactory.sol

**Scopul**: Creează clone minimaliste (EIP-1167) ale RoyaltySplitter pentru eficiență de gaz.

#### EIP-1167 Minimal Proxy

Fiecare NFT are propul splitter, creat ca o copie de cod minimal care delegă apelurile la implementarea originală. Aceasta economisește gaz comparat cu deployment complet.

#### Funcții Principale

##### 1. `createSplitter(...)`

```solidity
function createSplitter(
    address creator,
    address treasury,
    uint16 creatorBps,
    uint16 treasuryBps
) external returns (address splitter)
```

**Logică:**
1. Clonează contractul de implementare
2. Apelează `init()` pe clona nou creată cu parametrii
3. Emite `SplitterCreated`

**Exemplu:**
```typescript
splitter = implementation.clone();  // Creează clone minimal
IRoyaltySplitter(splitter).init(creator, treasury, 4000, 6000);
```

##### 2. `updateSplitterTreasury(address splitter, address newTreasury)`

Admin-ul fabricii poate actualiza treasury-ul pentru orice splitter.

---

## Fluxuri de Tranzacții

### Flux 1: Creare și Listare NFT

```
┌─────────────┐
│  Creator    │
└──────┬──────┘
       │
       │ (apelează mint)
       ▼
┌──────────────────────────────────────┐
│ 1. StrDomainsNFT.mint(creator, uri,  │
│    "vip.domains.str")                │
└────────┬─────────────────────────────┘
         │
         ├─→ Creează NFT (token #123)
         │
         ├─→ Stochează mapping: "vip.domains.str" → 123
         │
         └─→ Apelează RoyaltySplitterFactory.createSplitter()
                  │
                  └─→ Creează RoyaltySplitter clona unica
                      │
                      └─→ Inițializează cu:
                          - creator: msg.sender
                          - treasury: treasury_address
                          - split: 40/60
```

### Flux 2: Vânzare pe Piață (ETH)

```
┌──────────────────────────────┐
│ Proprietar NFT (Seller)      │
└───────────┬──────────────────┘
            │
            │ (1) approve Marketplace
            ▼
    ┌──────────────┐
    │ NFT Contract │
    └──────────────┘
            │
            │ (2) listToken(nft, 123, 100 ETH)
            ▼
    ┌────────────────────────────┐
    │ Marketplace               │
    │ - Transfer NFT în escrow   │
    │ - Creează listing #42      │
    └────────────────────────────┘
            │
            │ Cumpărător plătește
            │
            ├─→ buy(42) {value: 100 ETH}
            │
            ├─→ Calculează:
            │   - Redevențe: 5 ETH
            │   - Comision: 2.5 ETH
            │   - Seller: 92.5 ETH
            │
            ├─→ Trimite 5 ETH → RoyaltySplitter
            │        │
            │        └─→ Splitter divide:
            │            - Creator: 2 ETH
            │            - Treasury: 3 ETH
            │
            ├─→ Acumulează 2.5 ETH în piață
            │
            ├─→ Trimite 92.5 ETH → Seller
            │
            └─→ Transferă NFT → Buyer
```

### Flux 3: Retragere Redevențe

```
┌──────────────────────────────┐
│ Creator/Treasury             │
└───────────┬──────────────────┘
            │
            │ withdraw() pe RoyaltySplitter
            ▼
    ┌──────────────────────────┐
    │ RoyaltySplitter          │
    │ - Citește ethBalance[]   │
    │ - Trimite balanța        │
    │ - Setează balanța = 0    │
    └──────────────────────────┘
            │
            ▼
    Creator/Treasury primesc ETH
```

---

## Instalare și Configurare

### Cerințe Preliminare

```bash
Node.js >= 16.0.0
yarn >= 1.22.0 (sau npm >= 8.0.0)
```

### Pași de Instalare

```bash
# 1. Clonează repository-ul
git clone <repository-url>
cd str-smart-contracts

# 2. Instalează dependențele
yarn install

# 3. Creează fișierul .env
cp .env.example .env
```

### Configurare .env

```bash
# Rețea RPC URLs
AMOY_RPC_URL=https://rpc-amoy.polygon.technology
POLYGON_RPC_URL=https://polygon-rpc.com

# Chei private (DOAR PENTRU DEV, NICIODATĂ ÎN PRODUCȚIE)
MNEMONIC="word1 word2 word3 ... word12"
PRIVATE_KEY_DEPLOY=0x...

# API Keys pentru verificare (Polygonscan)
POLYGONSCAN_API_KEY=your_api_key_here

# Configurare deployment
PRODUCTION=false
MARKETPLACE_TREASURY=0x...
NFT_ROYALTY_TREASURY=0x...
FEE_MARKETPLACE_BPS=250  # 2.5%
ROYALTY=500  # 5%
```

### Compilare Contracte

```bash
yarn compile
```

Output:
```
Compiled 4 Solidity files successfully
```

### Rulare Teste

```bash
# Toate testele
yarn test

# Test specific
yarn testMarketplace

# Cu coverage
npx hardhat coverage
```

---

## Deployment

### Deployment Local (Hardhat Node)

```bash
# Terminal 1: Start local node
yarn local:node

# Terminal 2: Deploy
yarn deploy:local
```

Output tipic:
```
Deployer: 0x...
RoyaltySplitter implementation: 0x...
RoyaltySplitterFactory: 0x...
StrDomainsNFT: 0x...
Marketplace: 0x...

DONE ✅
NFT_ROYALTY_TREASURY=0x...
MARKETPLACE_TREASURY=0x...
```

### Deployment Amoy Testnet

```bash
# Setează PRODUCTION=false în .env
yarn deploy:amoy
```

### Deployment Polygon Mainnet

```bash
# Setează PRODUCTION=true în .env
# Asigură-te că PRIVATE_KEY_DEPLOY este setat și are suficiente fonduri
yarn deploy:polygon
```

### Fișierul de Deployment (deploy_registry.ts)

Script-ul automatizează:
1. Deployment RoyaltySplitter implementation
2. Deployment RoyaltySplitterFactory (cu referință la implementare)
3. Deployment StrDomainsNFT (cu referință la fabrică)
4. Deployment Marketplace
5. Grant SALES_ROLE la Marketplace pe NFT contract

---

## Integrare și Utilizare

### Integrare pentru Dapp-uri

#### 1. Mint NFT (Doar Admin)

```javascript
const contract = new ethers.Contract(NFT_ADDRESS, NFT_ABI, signer);

const txMint = await contract.mint(
    "0x742d35Cc6634C0532925a3b844Bc7e7bEe0b5aBC", // to address
    "ipfs://QmXxxx...",                              // metadata URI
    "my-domain.str"                                  // domain name
);

const receipt = await txMint.wait();
const tokenId = receipt.events[0].args.tokenId;
```

#### 2. Listare pentru Vânzare

```javascript
const nftContract = new ethers.Contract(NFT_ADDRESS, NFT_ABI, signer);
const marketplaceContract = new ethers.Contract(MARKETPLACE_ADDRESS, MARKETPLACE_ABI, signer);

// Step 1: Approve marketplace
const approveTx = await nftContract.approve(MARKETPLACE_ADDRESS, tokenId);
await approveTx.wait();

// Step 2: List for sale
const listTx = await marketplaceContract.listToken(
    NFT_ADDRESS,
    tokenId,
    ethers.parseEther("10") // 10 ETH
);

const listReceipt = await listTx.wait();
const listingId = listReceipt.events[0].args.listingId;
```

#### 3. Cumpărare NFT

```javascript
const buyTx = await marketplaceContract.buy(listingId, {
    value: ethers.parseEther("10")
});

await buyTx.wait();
// Buyer primește NFT automat!
```

#### 4. Retragere Redevențe

```javascript
const splitterAddress = await nftContract.splitters(tokenId);
const splitterContract = new ethers.Contract(splitterAddress, SPLITTER_ABI, signer);

// Creator retrage
const withdrawTx = await splitterContract.withdraw();
await withdrawTx.wait();
```

#### 5. Admin Retrage Comisioane Piață

```javascript
const adminTx = await marketplaceContract.withdrawFees();
await adminTx.wait();
```

### Exemplo Complet: Workflow Complet

```javascript
import { ethers } from "ethers";

// Setup
const provider = new ethers.JsonRpcProvider("https://rpc-amoy.polygon.technology");
const signer = new ethers.Wallet(PRIVATE_KEY, provider);

const nftContract = new ethers.Contract(NFT_ADDRESS, NFT_ABI, signer);
const marketplaceContract = new ethers.Contract(MARKETPLACE_ADDRESS, MARKETPLACE_ABI, signer);

async function fullWorkflow() {
    // 1. Mint NFT
    console.log("1. Minting NFT...");
    const mintTx = await nftContract.mint(
        await signer.getAddress(),
        "ipfs://QmExample",
        "my-awesome-domain.str"
    );
    const mintReceipt = await mintTx.wait();
    const tokenId = mintReceipt.logs[0].topics[3]; // Get tokenId from event
    console.log("✓ NFT minted, tokenId:", tokenId);

    // 2. Approve marketplace
    console.log("2. Approving marketplace...");
    const approveTx = await nftContract.approve(MARKETPLACE_ADDRESS, tokenId);
    await approveTx.wait();
    console.log("✓ Marketplace approved");

    // 3. List for sale
    console.log("3. Listing for sale...");
    const listTx = await marketplaceContract.listToken(
        NFT_ADDRESS,
        tokenId,
        ethers.parseEther("5") // 5 MATIC
    );
    const listReceipt = await listTx.wait();
    const listingId = listReceipt.logs[0].topics[1];
    console.log("✓ Listed with ID:", listingId);

    // 4. Someone buys
    console.log("4. Buyer purchasing...");
    const buyerSigner = new ethers.Wallet(BUYER_PRIVATE_KEY, provider);
    const buyerMarketplace = marketplaceContract.connect(buyerSigner);
    const buyTx = await buyerMarketplace.buy(listingId, {
        value: ethers.parseEther("5")
    });
    await buyTx.wait();
    console.log("✓ NFT sold!");

    // 5. Withdraw royalties
    console.log("5. Withdrawing royalties...");
    const splitterAddress = await nftContract.royaltyInfo(tokenId, ethers.parseEther("1"));
    const splitterContract = new ethers.Contract(
        splitterAddress[0],
        SPLITTER_ABI,
        signer
    );
    const withdrawTx = await splitterContract.withdraw();
    await withdrawTx.wait();
    console.log("✓ Royalties withdrawn!");
}

fullWorkflow().catch(console.error);
```

---

## Securitate și Considerații

### Mecanisme de Securitate Implementate

#### 1. **Control de Acces (AccessControl)**
- Roluri MINTER_ROLE și SALES_ROLE pentru funcții critice
- Admin role pentru funcții administrative
- Protecție împotriva apelurilor neautorizate

#### 2. **ReentrancyGuard**
Implementat pe funcții care transferă fonduri:
- `buy()` și `buyWithERC20()`
- `withdraw()` și `withdrawToken()`
- `cancelListing()`

Previne atacuri de reentrancy care ar putea permite retrageri multiple.

#### 3. **EIP-1167 Clones pentru Economie de Gaz**
- Fiecare token are propriul splitter
- Minimizeaza overhead storage
- Reduce costuri de deployment

#### 4. **EIP-2981 Royalty Standard**
- Redevențe standardizate și descoperibile
- Compatibilitate cu piețele generale

#### 5. **Validări Riguroase**
- Verificare non-zero addresses
- Verificare balanțe înainte de transfer
- Verificare proprietate NFT
- Verificare split sums = 100%

### Riscuri și Mitigation

| Risc | Mitigation |
|------|-----------|
| Private key compromise | Folosește key management service, niciodată în plaintext |
| Rugpull pe trezorerie | Multi-sig wallet, timelock, governance |
| Flash loan attack | ReentrancyGuard, sanity checks pe prețuri |
| Domain takeover | Domenii sunt unice și mappings sunt persistente |
| Token incomplet ERC20 | SafeERC20 wrapper, try-catch pe deposits |

### Checkliste Securitate pentru Producție

```
[ ] Audit de securitate de la firma externă
[ ] Deployment pe testnet înainte de mainnet
[ ] Multi-sig wallet pentru admin role
[ ] Timelock pe schimbări de parametri critici
[ ] Monitoring și alerting pe contracte
[ ] Documentare completă a ratelor și parametrilor
[ ] Plan de urgenție și pauzare (pause mechanism)
[ ] Verificare USDC/stablecoin integrare
```

---

## FAQ și Troubleshooting

### Întrebări Frecvente

#### Q1: Care este diferența dintre creator și treasury?
**A:** 
- **Creator**: Persoană care a mint-at NFT-ul, primește 40% din redevențe
- **Treasury**: Adresă în proprietatea protocolului, primește 60% din redevențe

#### Q2: Pot să reduc rata de redevență?
**A:** Nu, este hardcoded la 5%. Pentru a schimba, ar trebui să deploy-ez un nou contract. Poți folosi governance pentru modificări viitoare.

#### Q3: Ce se întâmplă dacă splitter-ul nu primește redevențe?
**A:** RoyaltySplitter are metoda `depositToken()` care poate fi apelată manual. De asemenea, acceptă ETH direct via `receive()`.

#### Q4: Pot lista același NFT pe mai multe piețe?
**A:** Doar pe Marketplace-ul oficial. Odată transferat la marketplace, nu poate fi transferat la altă piață până nu este cumpărat sau anulată listarea.

#### Q5: Cum verific cine este creatorul unui token?
**A:** 
```javascript
const creator = await nftContract.creatorOf(tokenId);
```

#### Q6: Pot transfera NFT-ul meu din piață fără a-l vinde?
**A:** Da, folosind `cancelListing(listingId)`. NFT-ul este imediat returnat în portofelul tău.

### Troubleshooting Common Issues

#### Problemă: "not owner" la listare
**Cauza:** Nu ești proprietar al NFT-ului
**Soluție:** Verifică adresa portofelului și asigură-te că deții NFT-ul

#### Problemă: "not approved" la listare
**Cauza:** Nu ai aprobat marketplace să transfere NFT-ul
**Soluție:** Apelează `approve()` pe contractul NFT mai întâi

#### Problemă: "bad value" la buy
**Cauza:** Valoarea ETH trimisă nu se potrivește cu preț listării
**Soluție:** Verifică preț cu `getListing()` și trimite exact acea valoare

#### Problemă: Comisioane nu sunt retrase
**Cauza:** Trebuie să fii admin și să apelezi `withdrawFees()`
**Soluție:** Apelează `withdrawFees()` dacă ești în admin role

#### Problemă: Domain "already exists"
**Cauza:** Domeniul a fost deja mint-at
**Soluție:** Folosește alt domain name, sunt indiferent case

---

## Configurări de Rețea

### Amoy Testnet (Polygon)

```
Chain ID: 80002
RPC: https://rpc-amoy.polygon.technology
Block Explorer: https://amoy.polygonscan.com
Faucet: https://faucet.polygon.technology
```

### Polygon Mainnet

```
Chain ID: 137
RPC: https://polygon-rpc.com
Block Explorer: https://polygonscan.com
```

### Hardhat Local

```
Chain ID: 31337
RPC: http://127.0.0.1:8545
Network: localhost
```

---

## Structura Fișierelor

```
str-smart-contracts/
├── contracts/
│   ├── StrDomainsNFT.sol           # ERC721 contract
│   ├── Marketplace.sol              # Piață descentralizată
│   ├── RoyaltySplitter.sol          # Divisor redevențe
│   └── RoyaltySplitterFactory.sol   # Fabrica de divisori
├── scripts/
│   ├── deploy_registry.ts           # Deployment script principal
│   ├── mint.ts                      # Minting script
│   ├── transfer.ts                  # Transfer script
│   └── burn.ts                      # Burn script
├── test/
│   ├── StrDomainsNFT.js             # Teste NFT contract
│   └── Marketplace.js               # Teste Marketplace
├── hardhat.config.ts                # Configurare Hardhat
├── package.json                     # Dependențe și scripts
├── tsconfig.json                    # Configurare TypeScript
└── README.md                        # Documentație engleză
```

---


## Versioni și Changelog

### v1.0.0 (februarie 2026)
- Release inițial
- 4 contracte inteligente
- Suport ETH și ERC-20
- Sistem complet de redevențe
- Piață funcțională


## Contact
Pentru întrebări suplimentare: 3JlblgEHb88@gmail.com