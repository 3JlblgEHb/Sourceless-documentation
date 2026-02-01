# Documentație proiect 

## 1. Prezentare generală
Acest proiect este o aplicație web front‑end construită cu **Next.js** (App Router) și **React**. Structura sugerează o platformă orientată pe domenii/marketplace, cu zone publice (home, blog, help-center, learning-hub) și zone autentificate (dashboard, admin).

## 2. Tehnologii și stack
- **Next.js** (App Router) — rutare pe bază de directoare în `src/app`.
- **React** — componente UI.
- **TypeScript** — tipare stricte în majoritatea componentelor și utilitarelor.
- **Tailwind CSS** — stilizare utilitară (fișiere: `tailwind.config.js`, `postcss.config.mjs`).
- **ESLint** — reguli de lint (`eslint.config.mjs`).
- **Supabase** — integrare DB/servicii (indicii în `src/db/supabase.ts`, `src/lib/supabaseClient.ts`).
- **Docker** — pentru build/rulare (`Dockerfile`, `docker-compose.yml`).

## 2.1 Dependențe principale (rezumat)
- **UI/Design system**: Radix UI, lucide-react, react-icons, class-variance-authority, clsx, tailwind-merge.
- **Stare & date**: @tanstack/react-query, SWR, Zustand.
- **Forme & validare**: react-hook-form, @hookform/resolvers, zod.
- **Editor**: TipTap (starter-kit + extensii).
- **Animații**: framer-motion, aos, embla-carousel-react.
- **Auth & securitate**: jsonwebtoken, bcryptjs, cookies-next.
- **Date/DB**: @supabase/supabase-js, mysql2.
- **UI diverse**: react-day-picker, recharts, sonner, react-toastify, vaul, input-otp.

## 2.2 Arhitectură (nivel înalt)
Aplicația este organizată pe straturi, cu separarea între:
1. **Rute & pagini** (App Router): `src/app`.
2. **Componente UI reutilizabile**: `src/components`, `src/ui`.
3. **Hook‑uri custom**: `src/hooks`.
4. **Servicii & acces date**: `src/services`, `src/db`, `src/lib`.
5. **Tipuri & utilitare**: `src/types`, `src/utils`.

### 2.2.1 Flux de rutare
- `src/app/layout.tsx` și `src/app/globals.css` definesc layout și stiluri globale.
- `src/app/middleware.ts` + `src/middleware.ts` gestionează logică transversală (ex: auth, headers).
- Rutele sunt structurate pe module: `blog`, `dashboard`, `admin`, `payment`, `user`.

### 2.2.2 Acces la date
- **Supabase**: clienți și inițializare în `src/db` și `src/lib`.
- **API routes**: `src/app/api/*` pentru endpoints server‑side.
- **Servicii**: `src/services` pentru integrare cu API/DB și mapări de date.

### 2.2.3 State management & cache
- Pentru date server: React Query și/sau SWR.
- Pentru stare UI: Zustand (ex: mini cart, UI toggles).

### 2.2.4 UI & stiluri
- Tailwind CSS + utilitare pentru compunere clase.
- Componente complexe în `src/components` și `src/ui`.
- Animații în `framer-motion` și `aos`.

## 3. Structura proiectului
```
public/                # resurse statice (html, imagini, video, sw.js)
src/
  app/                 # App Router (pagini, layout-uri, middleware)
    admin/             # pagini administrare
    api/               # rute API (server actions / handlers)
    blog/              # pagini blog + componente
    dashboard/         # zonă autentificată + submodule
    help-center/       # pagini suport
    learning-hub/      # pagini educaționale
    payment/           # pagini pentru plăți
    user/              # auth, resetare parolă
  assets/              # iconuri/imagini/video
  components/          # componente reutilizabile
  config/              # configurații (ex: app.config.ts)
  constants/           # constante globale
  db/                  # integrare DB (Supabase)
  hooks/               # custom React hooks
  lib/                 # utilitare și context
  services/            # integrare API/DB/mappers
  types/               # tipuri TypeScript
  ui/                  # componente UI comune
  utils/               # helpers

configs în rădăcină:
- next.config.js, tsconfig.json, tailwind.config.js
- eslint.config.mjs, postcss.config.mjs
- docker-compose.yml, Dockerfile
```

## 4. Flux de lucru recomandat
- **Dezvoltare locală**: rulați aplicația cu managerul de pachete folosit în proiect (npm/yarn/pnpm). Verificați `package.json` pentru scripturi.
- **Lint & format**: folosiți scripturile ESLint disponibile.
- **Build**: folosiți scriptul `build` din `package.json`.

## 4.1 Business‑flow (nivel înalt)
> Notă: fluxurile exacte trebuie validate în UI și cu product owner.

1. **Vizitator public**
   - Accesează Home, Blog, Help Center, Learning Hub.
   - Consumă conținut de tip articole/categorii.
2. **Căutare domenii**
   - Zona `dashboard/domain-search` sugerează flux de căutare și listare domenii.
   - Se poate ajunge în `domains/marketplace` pentru oferte/pachete.
3. **Checkout & plată**
   - `dashboard/checkout` + `payment/success|error` pentru procesarea plăților.
4. **Autentificare & cont**
   - `user/auth`, `forgotPassword`, `resetPassword` pentru gestionarea contului.
5. **Admin & conținut**
   - `admin/article`, `admin/blog`, `admin/category` pentru management conținut.
   - Editorul TipTap este folosit pentru conținut rich‑text.

## 5. Zone cheie pentru întreținere
1. **Rutele din `src/app`**
   - fiecare folder reprezintă o rută. Exemple: `blog`, `dashboard`, `admin`, `help-center`.
2. **Componente UI comune**
   - în `src/components` și `src/ui` se află majoritatea componentelor reutilizabile.
3. **Integrarea cu Supabase**
   - verificați `src/db/supabase.ts` și `src/lib/supabaseClient.ts` pentru conexiune și configurare.
4. **Servicii & mappers**
   - `src/services` conține logica de conectare la API/DB și mapări de date.

## 6. Configurări și variabile de mediu
- Verificați dacă există variabile de mediu necesare (ex: pentru Supabase, API endpoints). Dacă lipsesc, creați un fișier `.env.local`.
- Cheile tipice: `NEXT_PUBLIC_*`, `SUPABASE_URL`, `SUPABASE_ANON_KEY` (exemple, confirmați în cod).

## 7. Recomandări pentru mentenanță
- Respectați convențiile de organizare: componentele comune în `components/ui`, paginile în `src/app`.
- Evitați duplicarea stilurilor: preferați Tailwind și clase utilitare.
- Mențineți tipurile în `src/types` actualizate.
- Testați rutele critice după modificări în `middleware`.

## 7.1 Puncte de atenție operaționale
- **Cache/invalidare**: dacă se folosesc React Query/SWR, definiți politici clare de revalidare.
- **Securitate**: token‑uri și cookie‑uri gestionate de `jsonwebtoken` și `cookies-next`.
- **Plăți**: logarea erorilor și fallback‑uri în `payment/error`.
- **Editor**: validate/escapare input pentru conținutul rich‑text.

## 8. Checklist la predare
- [ ] Asigurați-vă că `.env` are toate variabilele necesare.
- [ ] Documentați orice integrare externă (servicii API, webhooks, plăți).
- [ ] Verificați că build-ul trece local.
- [ ] Revizuiți permisiunile pentru zonele `admin` și `dashboard`.

---

## Contact
Pentru întrebări suplimentare: 3JlblgEHb88@gmail.com