# Ghid Tehnic Complet pentru Proiectul Android

## 0. Prezentare generală
Acest document oferă toate informațiile necesare pentru predarea completă a proiectului Android „Sourceless Wallet” după finalizarea dezvoltării.

## 1. Funcționalitate și User Flows
- Creare portofel nou (seed phrase)
- Import portofel (seed phrase/private key)
- Vizualizare solduri și tranzacții (Ethereum, BSC, Polygon, Arbitrum)
- Trimitere/primire criptomonede (QR, copiere adresă, confirmare biometrică)
- Integrare WalletConnect
- Schimb valutar (swap)
- Securitate: blocare cu biometrie

## 2. Principalele ecrane (Screens)
- Onboarding
- Dashboard
- Detalii portofel
- Trimitere/primire
- Swap
- Setări
- Autorizare biometrică
- WalletConnect

## 3. Tehnologii și biblioteci principale
- React Native (0.81.5)
- Expo (~54.0.0)
- TypeScript (~5.9.2)
- expo-camera, expo-local-authentication, expo-secure-store, expo-font, expo-dev-client
- ethers.js, bitcoinjs-lib, @walletconnect/core
- react-navigation, react-native-svg, react-native-reanimated
- TailwindCSS (nativewind)
- Gradle, Android SDK
- Hermes (engine JS)

## 4. Use-case-uri principale
- Creare/import portofel
- Scanare/generare QR
- Autentificare biometrică
- Swap
- WalletConnect
- Multi-chain

## 5. Structura proiectului
- src/ — cod sursă JS/TS
- android/ — fișiere native, build.gradle, manifest, assets
- assets/ — iconițe, imagini
- docs/ — documentație
- package.json, app.json, eas.json, app.config.js — configurări principale

## 6. Configurare și instalare
1. Clonați repository-ul:
   ```sh
   git clone <adresa-repo>
   ```
2. Schimbați pe ramura Android:
   ```sh
   git checkout android
   ```
3. Instalați dependențele JS:
   ```sh
   npm install
   ```

## 7. Configurare fișiere importante
- app.json: parametri cheie pentru Android
- android/app/build.gradle: applicationId, versiuni, permisiuni
- AndroidManifest.xml: permisiuni, activități
- proguard-rules.pro, gradle.properties
- eas.json: build config
- package.json: dependențe JS
- app.config.js: configurări Expo

## 8. Build și rulare
- Pentru dezvoltare locală pe emulator/dispozitiv:
  ```sh
  npm run android
  ```
- Pentru build de producție (Google Play):
  ```sh
  eas build --profile production --platform android
  ```

## 9. Testare
- Teste JS/TS cu Jest
- Testare nativă cu Android Studio

## 10. Probleme frecvente și soluții
- Probleme cu dependențele native: rulați `npm run android` sau `./gradlew clean`
- Probleme de semnare: verificați keystore și config
- Emulatorul nu pornește: verificați Android Studio și SDK

## 11. Recomandări
- Folosiți variabile de mediu pentru chei sensibile
- Documentați orice modificare majoră
- Actualizați dependențele periodic

## 12. Suport rețele și extindere
- Ethereum, BSC, Polygon, Arbitrum — configurate în src/constants/
- Pentru rețea nouă: adăugați config, actualizați UI, testați

## 13. Contact
Pentru întrebări suplimentare: 3JlblgEHb88@gmail.com

## 14. Semnare și gestionare keystore (release)

### Ce este keystore?
Keystore-ul Android este un fișier criptat care conține cheia privată folosită pentru semnarea aplicației la build-ul de producție (release). Fără acest fișier, nu se pot publica actualizări pentru aceeași aplicație în Google Play.

### Generare keystore nou
Rulați în terminal:
```sh
keytool -genkeypair -v -keystore release.keystore -alias your_key_alias -keyalg RSA -keysize 2048 -validity 10000
```
Urmați instrucțiunile și salvați parola, alias-ul și locația fișierului.

### Configurare keystore în proiect
1. Copiați `release.keystore` în `android/app/`.
2. În `android/app/build.gradle` adăugați la `signingConfigs`:
   ```gradle
   release {
      storeFile file('release.keystore')
      storePassword 'parola_keystore'
      keyAlias 'your_key_alias'
      keyPassword 'parola_cheie'
   }
   ```
3. În secțiunea `buildTypes`:
   ```gradle
   release {
      signingConfig signingConfigs.release
      ...
   }
   ```

### Utilizare cu EAS Build
Dacă folosiți EAS Build, puteți încărca keystore-ul cu:
```sh
eas credentials
```
și urmați pașii pentru a importa sau genera automat keystore

### Recomandări
- Salvați o copie de rezervă a keystore-ului și a parolelor într-un loc sigur.
- Nu publicați niciodată keystore-ul în repository public.
- Fără keystore, nu veți putea actualiza aplicația în Google Play!


## Contact
Pentru întrebări suplimentare: 3JlblgEHb88@gmail.com