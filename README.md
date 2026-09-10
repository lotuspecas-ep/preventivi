# Lotus Pecas — Copia Commissioni

Web app autonoma (singolo file `index.html`) per creare, firmare (FES rinforzata),
esportare in PDF e archiviare le Copie Commissioni di Lotus Pecas Italia.

## Accesso
- Team Lotus Pecas Italia — utente `TEAMLPITA` · password `LP2026`
- Rivenditore / Reseller — utente `RESLPITA` · password `LP2026`

> Nota: il login attuale è un gate lato client (le credenziali sono nel file).
> Non è sicurezza forte. Passaggio consigliato: Supabase Auth.

## Pubblicare su GitHub Pages
1. Crea una nuova repository su GitHub (vuota).
2. Carica `index.html` (drag & drop nella pagina della repo, oppure via git):
   ```bash
   git init
   git add index.html README.md
   git commit -m "LP Copia Commissioni"
   git branch -M main
   git remote add origin https://github.com/<utente>/<repo>.git
   git push -u origin main
   ```
3. Repo → **Settings → Pages** → Source: `Deploy from a branch` → Branch: `main` / `/root` → Save.
4. Dopo qualche minuto il sito è online su `https://<utente>.github.io/<repo>/`.

## Collegare Supabase (archivio cloud)
1. Crea il progetto su Supabase. **Settings → API**: copia **Project URL** e **anon public key**.
2. Supabase → **SQL Editor** → esegui:
   ```sql
   create table if not exists documenti (
     id text primary key,
     status text, created_at timestamptz, updated_at timestamptz,
     numero text, data text, cliente text, importo numeric,
     pay_mode text, plan text,
     signed jsonb, hash text, role text, creator jsonb, payload jsonb
   );
   alter table documenti enable row level security;
   -- Policy PROVVISORIA: chiunque abbia la anon key può leggere/scrivere.
   create policy anon_all on documenti for all to anon using (true) with check (true);
   ```
3. Apri il sito → **Home → Impostazioni cloud (Supabase)** → incolla URL + anon key → **Connetti**.
   (In alternativa si possono cablare i valori direttamente nel file per connessione automatica.)

## Dati
- Archivio locale (localStorage) sempre attivo: bozze + emessi per-browser.
- Con Supabase connesso: sincronizzazione locale ↔ cloud, accessibile da ogni dispositivo.
- Ogni documento salva anche l'impronta di integrità SHA-256.
- Backup manuale: **Home → Esporta / Importa backup (JSON)**.
