# Konto- und Cloud-Synchronisierung einrichten

Die App nutzt jetzt E-Mail/Passwort-Login über Supabase. Nutzer müssen keinen GitHub-Token mehr eintragen: Sie erstellen ein Konto, melden sich auf einem anderen Gerät wieder an und sehen ihre eigene Konzertliste.

## Website-Dateien veröffentlichen

Für Excel-Import, Installation und Offline-Nutzung müssen alle Dateien aus diesem Ordner im selben Ordner wie die `index.html` liegen:

- `index.html`
- `xlsx.full.min.js`
- `manifest.webmanifest`
- `sw.js`
- `icon-192.png`, `icon-512.png`, `icon-192.svg` und `icon-512.svg`

Nach dem Hochladen die veröffentlichte Seite einmal neu laden. Die App kann anschließend über **App installieren** oder über das Browsermenü zum Startbildschirm hinzugefügt werden.

## 1. Supabase-Projekt anlegen

1. Auf [supabase.com](https://supabase.com) ein neues Projekt erstellen.
2. Unter **Project Settings → API** diese Werte kopieren:
   - **Project URL**
   - **anon public key**
3. In `index.html` oben im Script diese beiden Konstanten füllen:

```js
const SUPABASE_URL = "https://dein-projekt.supabase.co";
const SUPABASE_ANON_KEY = "dein-anon-public-key";
```

Der `anon public key` darf in einer statischen Website stehen. Die Trennung der Nutzer passiert durch Row-Level-Security in der Datenbank.

## 2. Datenbank-Tabelle anlegen

In Supabase **SQL Editor → New query** öffnen und ausführen:

```sql
create table if not exists public.concert_archives (
  user_id uuid primary key references auth.users(id) on delete cascade,
  payload jsonb not null default '{}'::jsonb,
  updated_at timestamptz not null default now()
);

alter table public.concert_archives enable row level security;

drop policy if exists "Users can read their own concert archive" on public.concert_archives;
drop policy if exists "Users can insert their own concert archive" on public.concert_archives;
drop policy if exists "Users can update their own concert archive" on public.concert_archives;

create policy "Users can read their own concert archive"
on public.concert_archives
for select
to authenticated
using ((select auth.uid()) = user_id);

create policy "Users can insert their own concert archive"
on public.concert_archives
for insert
to authenticated
with check ((select auth.uid()) = user_id);

create policy "Users can update their own concert archive"
on public.concert_archives
for update
to authenticated
using ((select auth.uid()) = user_id)
with check ((select auth.uid()) = user_id);
```

## 3. Login für Nutzer aktivieren

Unter **Authentication → Providers → Email** muss E-Mail aktiviert sein.

Für die einfachste private Nutzung kannst du **Confirm email** deaktivieren. Dann ist ein neues Konto sofort nutzbar. Wenn du E-Mail-Bestätigung aktiviert lässt, zeigt die App nach der Registrierung an, dass die E-Mail zuerst bestätigt werden muss.

## 4. Nutzung in der App

1. Website öffnen.
2. Oben **Anmelden** wählen.
3. **Konto erstellen** oder mit bestehendem Konto anmelden.
4. Danach wird jede Änderung automatisch gespeichert.
5. Auf einem weiteren Gerät einfach mit derselben E-Mail und demselben Passwort anmelden.

Wenn bereits lokale Konzerte vorhanden sind und die Cloud-Liste noch leer ist, legt die App beim ersten Login automatisch die erste Cloud-Kopie an.

## Sicherheit und Verhalten

- Jede Person sieht nur den eigenen Datensatz, weil `user_id = auth.uid()` erzwungen wird.
- Ohne Anmeldung speichert die App weiter nur lokal auf dem Gerät.
- Die lokale Offline-Kopie bleibt pro angemeldetem Nutzer getrennt gespeichert.
- Über **Cloud-Liste laden** und **Dieses Gerät hochladen** kann der Abgleich manuell ausgelöst werden.
