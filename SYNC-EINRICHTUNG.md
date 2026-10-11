# Online-Speicherung ohne Konto

Die App speichert deine Konzertliste online in Supabase, aber ohne Benutzerkonten. Alle Geräte laden und bearbeiten dieselbe Liste. Zusätzlich gibt es einen kostenlosen GitHub-Actions-Keepalive, damit das Supabase-Projekt regelmäßig Datenbankaktivität bekommt.

## Website-Dateien veröffentlichen

Für Excel-Import, Installation und Offline-Nutzung müssen alle Dateien aus diesem Ordner im selben Ordner wie die `index.html` liegen:

- `index.html`
- `xlsx.full.min.js`
- `manifest.webmanifest`
- `sw.js`
- `icon-192.png`, `icon-512.png`, `icon-192.svg` und `icon-512.svg`

Nach dem Hochladen die veröffentlichte Seite einmal neu laden.

## Supabase-Tabelle anlegen

In Supabase **SQL Editor -> New query** öffnen und ausführen:

```sql
create table if not exists public.shared_concert_archive (
  id text primary key,
  payload jsonb not null default jsonb_build_object(
    'version', 2,
    'concerts', jsonb_build_array(),
    'venues', jsonb_build_array(),
    'bands', jsonb_build_array(),
    'preferences', jsonb_build_object()
  ),
  updated_at timestamptz not null default now()
);

alter table public.shared_concert_archive enable row level security;

drop policy if exists "Shared concert archive can be read" on public.shared_concert_archive;
drop policy if exists "Shared concert archive can be created" on public.shared_concert_archive;
drop policy if exists "Shared concert archive can be updated" on public.shared_concert_archive;

create policy "Shared concert archive can be read"
on public.shared_concert_archive
for select
to anon
using (id = 'jakob-konzerte');

create policy "Shared concert archive can be created"
on public.shared_concert_archive
for insert
to anon
with check (id = 'jakob-konzerte');

create policy "Shared concert archive can be updated"
on public.shared_concert_archive
for update
to anon
using (id = 'jakob-konzerte')
with check (id = 'jakob-konzerte');

insert into public.shared_concert_archive (id)
values ('jakob-konzerte')
on conflict (id) do nothing;
```

## Keepalive gegen Supabase-Pause

Im Ordner `.github/workflows/` liegt die Datei `supabase-keepalive.yml`. Wenn diese Website in einem GitHub-Repository liegt und GitHub Actions aktiviert ist, ruft GitHub Supabase einmal täglich auf. Das erzeugt regelmäßig Datenbankaktivität und hilft, das kostenlose Supabase-Projekt wach zu halten.

Wichtig: Supabase garantiert das dauerhafte Nicht-Pausieren auf dem Free-Plan nicht offiziell. Für eine private kleine Website ist dieser Keepalive aber die kostenlose praktische Lösung.

## Verhalten in der App

- Beim Öffnen lädt die App die Online-Liste.
- Änderungen werden automatisch online gespeichert.
- Eine lokale Kopie bleibt im Browser erhalten, falls Supabase kurz nicht erreichbar ist.
- Über **Jetzt online speichern** und **Online-Liste laden** kannst du manuell synchronisieren.
- Für zusätzliche Sicherheit kannst du weiterhin gelegentlich **Excel-Export** nutzen.

Hinweis: Ohne Login gibt es keinen echten privaten Zugriffsschutz. Wer den Website-Code und den öffentlichen Supabase-Key kennt, könnte theoretisch dieselbe Liste lesen oder ändern. Für starken Schutz wären wieder Konten oder eine kleine geschützte Server-Funktion nötig.
