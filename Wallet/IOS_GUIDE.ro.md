
### Rețele suportate implicit
- Ethereum (ETH)
- Binance Smart Chain (BSC)
- Polygon (MATIC)
- Arbitrum

### Cum funcționează adăugarea rețelelor
Lista rețelelor suportate este definită în fișierele de configurare și/sau în codul sursă (ex: `src/constants/`, `src/context/`, `src/services/`). Pentru fiecare rețea sunt specificate:
- chainId
- denumire
- simbol nativ
- RPC endpoint
- block explorer
- contracte suportate (opțional)

### Adăugarea unei noi rețele
1. Adăugați configurația rețelei noi în fișierul corespunzător (ex: `src/constants/networks.ts`).
2. Actualizați componentele de selectare a rețelei (ex: NetworkSelectorModal).
3. Asigurați-vă că RPC-ul și block explorer-ul sunt funcționale.
4. (Opțional) Adăugați suport pentru tokenuri specifice rețelei.
5. Testați trimiterea, primirea și afișarea soldului pentru noua rețea.

#### Exemplu de structură pentru o rețea nouă
```ts
{
   chainId: 12345,
   name: 'NouaRețea',
   symbol: 'NEW',
   rpcUrl: 'https://rpc.nouaretea.org',
   explorerUrl: 'https://explorer.nouaretea.org',
}
```

### Observații
- Pentru ca o rețea să fie complet funcțională, trebuie să existe suport în backend (dacă este cazul) și să fie adăugate eventuale reguli de validare pentru adrese și tranzacții.
- UI-ul permite selectarea rețelei și afișează soldul/tokenurile pentru fiecare rețea adăugată.

...existing code...


# Ghid Tehnic Complet pentru Proiectul iOS

## 0. Prezentare generală
Acest document oferă toate informațiile necesare pentru predarea completă a proiectului iOS „Sourceless Wallet” după finalizarea dezvoltării.

## 1. Funcționalitate și User Flows
- **Creare portofel nou**: utilizatorul poate genera un portofel nou cu seed phrase.
- **Import portofel**: import prin seed phrase sau private key.
- **Vizualizare solduri și tranzacții**: pentru rețelele Ethereum, BSC, Polygon, Arbitrum.
- **Trimitere și primire criptomonede**: scanare QR, copiere adresă, confirmare tranzacție cu biometrie.
- **Integrare WalletConnect**: conectare la DApps.
- **Schimb valutar (swap)**: integrare Uniswap (dacă este activă).
- **Securitate**: blocare aplicație cu Face ID/Touch ID.

## 2. Principalele ecrane (Screens)
- Onboarding (creare/import portofel)
- Dashboard (solduri, rețele)
- Detalii portofel (adrese, QR)
- Trimitere/primire (send/receive)
- Swap (schimb valutar)
- Setări (securitate, preferințe)
- Ecran de autorizare biometrică
- Ecran de conectare WalletConnect

## 3. Tehnologii și biblioteci principale
- **React Native** (0.81.5)
- **Expo** (~54.0.0)
- **TypeScript** (~5.9.2)
- **expo-camera**, **expo-local-authentication**, **expo-secure-store**, **expo-font**, **expo-dev-client**
- **ethers.js**, **bitcoinjs-lib**, **@walletconnect/core**
- **react-navigation**, **react-native-svg**, **react-native-reanimated**
- **TailwindCSS** (prin nativewind)
- **CocoaPods** (pentru dependențe native)
- **Hermes** (engine JS)

## 4. Use-case-uri principale
- Creare și import portofel (seed phrase, private key)
- Scanare și generare QR pentru adrese
- Autentificare biometrică pentru acces rapid și semnare tranzacții
- Schimb valutar direct din aplicație (swap)
- Conectare la DApps prin WalletConnect
- Gestionare multi-chain (Ethereum, BSC, Polygon, Arbitrum)

## 5. Structura, configurare, build, testare, probleme, recomandări
...existing code...

## 1. Introducere
Sourceless Wallet este un portofel non-custodial pentru criptomonede (Ethereum, BSC, Polygon, Arbitrum) cu gestionare completă a cheilor private pe dispozitiv. Aplicația este dezvoltată cu React Native/Expo și suportă build nativ pentru iOS.

## 2. Structura Proiectului
- `src/` — codul sursă principal (componente, ecrane, hooks, servicii)
- `ios/` — proiectul Xcode, fișiere native, storyboard, assets
- `assets/` — iconițe, imagini
- `docs/` — documentație
- `package.json`, `app.json`, `eas.json`, `app.config.js` — configurări principale

## 3. Configurare și Instalare
1. Clonați repository-ul:
    ```sh
    git clone <adresa-repo>
    ```
2. Schimbați pe ramura iOS:
    ```sh
    git checkout ios
    ```
3. Instalați dependențele JS:
    ```sh
    npm install
    ```
4. Instalați dependențele native:
    ```sh
    cd ios
    pod install
    ```

## 4. Configurare fișiere importante

### 4.1. app.json
```json
{
   "expo": {
      "name": "Sourceless Wallet",
      "slug": "crypto-wallet-mvp",
      "version": "1.1.0",
      "orientation": "portrait",
      "icon": "./assets/icon.png",
      "userInterfaceStyle": "automatic",
      "description": "Sourceless Wallet is a non-custodial cryptocurrency wallet for Ethereum, BSC, Polygon, and Arbitrum. Securely manage your digital assets with complete control over your private keys.",
      "splash": {
         "image": "./assets/splash-icon.png",
         "resizeMode": "contain",
         "backgroundColor": "#0F0C29"
      },
      "platforms": ["ios", "android"],
      "ios": {
         "supportsTablet": false,
         "bundleIdentifier": "net.sourceless.wallet",
         "buildNumber": "11",
         "infoPlist": {
            "NSFaceIDUsageDescription": "Sourceless Wallet uses Face ID to securely unlock your wallet and authorize transactions. Your biometric data never leaves your device and is processed only by iOS system APIs.",
            "NSCameraUsageDescription": "Sourceless Wallet uses your camera exclusively to scan QR codes for wallet addresses and transaction data. No photos or videos are saved. Camera access is only active when you use the QR scanner feature.",
            "ITSAppUsesNonExemptEncryption": false
         },
         "config": {
            "usesNonExemptEncryption": false
         }
      },
      "plugins": ["expo-secure-store", "expo-local-authentication", "expo-font", "expo-camera"]
   }
}
```

### 4.2. app.config.js
```js
module.exports = {
   expo: {
      name: 'Sourceless Wallet',
      slug: 'crypto-wallet-mvp',
      version: '1.1.0',
      icon: './assets/icon.png',
      splash: {
         image: './assets/splash-icon.png',
         resizeMode: 'contain',
         backgroundColor: '#0F0C29',
      },
      userInterfaceStyle: 'automatic',
      updates: {
         url: 'https://u.expo.dev/3e5c2c5b-c1a3-4c5d-b110-2b60ef5476cd',
      },
      runtimeVersion: {
         policy: 'appVersion',
      },
      scheme: 'sourcelesswallet',
      ios: {
         bundleIdentifier: 'net.sourceless.wallet',
         infoPlist: {
            ITSAppUsesNonExemptEncryption: false,
            NSCameraUsageDescription: 'Camera access is required to scan QR codes for transactions',
            NSFaceIDUsageDescription: 'Use Face ID for quick and secure access to your Sourceless Wallet',
         },
      },
      plugins: [
         [
            'expo-dev-client',
            {
               addGeneratedScheme: false,
            },
         ],
         'expo-camera',
      ],
      extra: {
         eas: {
            projectId: '3e5c2c5b-c1a3-4c5d-b110-2b60ef5476cd',
         },
      },
   },
};
```

### 4.3. Info.plist (SourcelessWallet/Info.plist)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<plist version="1.0">
<dict>
   <key>CFBundleDisplayName</key>
   <string>Sourceless Wallet</string>
   <key>CFBundleIdentifier</key>
   <string>net.sourceless.wallet</string>
   <key>CFBundleShortVersionString</key>
   <string>1.1.0</string>
   <key>CFBundleVersion</key>
   <string>11</string>
   <key>ITSAppUsesNonExemptEncryption</key>
   <false/>
   <key>NSCameraUsageDescription</key>
   <string>Camera access is required to scan QR codes for transactions</string>
   <key>NSFaceIDUsageDescription</key>
   <string>Use Face ID for quick and secure access to your Sourceless Wallet</string>
   <key>NSMicrophoneUsageDescription</key>
   <string>Allow $(PRODUCT_NAME) to access your microphone</string>
   <key>UILaunchStoryboardName</key>
   <string>SplashScreen</string>
   <key>UIRequiredDeviceCapabilities</key>
   <array>
      <string>arm64</string>
   </array>
   <key>UISupportedInterfaceOrientations</key>
   <array>
      <string>UIInterfaceOrientationPortrait</string>
      <string>UIInterfaceOrientationPortraitUpsideDown</string>
   </array>
   <key>UIUserInterfaceStyle</key>
   <string>Automatic</string>
</dict>
</plist>
```

### 4.4. PrivacyInfo.xcprivacy
```xml
<?xml version="1.0" encoding="UTF-8"?>
<plist version="1.0">
<dict>
   <key>NSPrivacyAccessedAPITypes</key>
   <array>
      <dict>
         <key>NSPrivacyAccessedAPIType</key>
         <string>NSPrivacyAccessedAPICategoryUserDefaults</string>
         <key>NSPrivacyAccessedAPITypeReasons</key>
         <array>
            <string>CA92.1</string>
         </array>
      </dict>
      <!-- ...alte categorii... -->
   </array>
   <key>NSPrivacyCollectedDataTypes</key>
   <array/>
   <key>NSPrivacyTracking</key>
   <false/>
</dict>
</plist>
```

### 4.5. SourcelessWallet.entitlements
```xml
<?xml version="1.0" encoding="UTF-8"?>
<plist version="1.0">
   <dict/>
</plist>
```

### 4.6. Expo.plist (updates OTA)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<plist version="1.0">
   <dict>
      <key>EXUpdatesCheckOnLaunch</key>
      <string>ALWAYS</string>
      <key>EXUpdatesEnabled</key>
      <true/>
      <key>EXUpdatesLaunchWaitMs</key>
      <integer>0</integer>
      <key>EXUpdatesRuntimeVersion</key>
      <string>1.1.0</string>
      <key>EXUpdatesURL</key>
      <string>https://u.expo.dev/3e5c2c5b-c1a3-4c5d-b110-2b60ef5476cd</string>
   </dict>
</plist>
```

### 4.7. Podfile și Podfile.lock
Configurare CocoaPods pentru dependențe native. Exemple:

**Podfile**
```ruby
platform :ios, '15.1'
target 'SourcelessWallet' do
   use_expo_modules!
   use_react_native!(
      :path => config[:reactNativePath],
      :hermes_enabled => true,
      :app_path => "#{Pod::Config.instance.installation_root}/..",
   )
end
```

**Podfile.lock** — vezi lista completă a dependențelor native generate automat după `pod install`.

### 4.8. Scheme și build settings (Xcode)
- Bundle Identifier: `net.sourceless.wallet`
- Deployment Target: iOS 15.1+
- Team ID: 42QW7FV7RS
- Swift Version: 5.0
- Enable Bitcode: NO
- Signing: automată sau manuală (după caz)

### 4.9. AppDelegate.swift
Codul principal pentru integrarea React Native și Expo în aplicația iOS.

### 4.10. eas.json
```json
{
   "build": {
      "production": {
         "ios": {
            "distribution": "store"
         }
      }
   },
   "submit": {
      "production": {
         "ios": {
            "appleId": "3jlblgehb88@gmail.com",
            "appleTeamId": "262KJ6922W"
         }
      }
   }
}
```

### 4.11. package.json (dependențe JS)
Include toate pachetele JS/TS folosite, inclusiv expo, react-native, expo-camera, expo-local-authentication, expo-secure-store, etc.

## 5. Build și rulare
1. Pentru dezvoltare locală pe simulator:
    ```sh
    npm run ios
    ```
2. Pentru build de producție (App Store):
    ```sh
    eas build --profile production --platform ios
    ```

## 6. Testare
Testele pot fi rulate cu Jest sau alte framework-uri JS/TS. Pentru testare nativă, folosiți Xcode.

## 7. Probleme frecvente și soluții
- Probleme cu dependențele native: rulați `pod install` în folderul ios.
- Probleme de semnare: verificați setările de provisioning profile și team ID.
- Simulatorul nu pornește: verificați dacă Xcode este actualizat și simulatorul configurat corect.

## 8. Recomandări
- Folosiți variabile de mediu pentru chei sensibile.
- Documentați orice modificare majoră în acest fișier.
- Actualizați dependențele periodic.

## Contact
Pentru întrebări suplimentare: 3JlblgEHb88@gmail.com