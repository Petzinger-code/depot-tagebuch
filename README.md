# Depot-Tagebuch

Web-App für das INX-Kalkulationstool: Positionen und Planpositionen in Euro (EZB-Kurs vom Kauftag),
Sektorverteilung Ist/Plan, Sektor-Check, Cash-Konten, Archiv sowie Import von INX-Excel, Degiro-CSV und Backups.

- Website: GitHub Pages (`.github/workflows/deploy.yml`, aktualisiert werktags auch die EZB-Kurse in `fx/`)
- Daten und Anmeldung: Supabase (`config.js`); jede Person sieht nur ihre eigenen Daten (Row Level Security)

## Datenbank einrichten (Supabase → SQL Editor)

```sql
create table public.docs (
  user_id uuid not null default auth.uid() references auth.users(id) on delete cascade,
  coll text not null,
  id text not null,
  data jsonb not null default '{}',
  updated_at timestamptz not null default now(),
  primary key (user_id, coll, id)
);
alter table public.docs enable row level security;
create policy "eigene Daten" on public.docs for all
  using (auth.uid() = user_id) with check (auth.uid() = user_id);
alter publication supabase_realtime add table public.docs;
```

Unter Authentication → URL Configuration die Adresse der Website als Site URL eintragen.
