# Enable cross-browser recipe sync

GitHub Pages is a static host, so browser storage alone cannot move data between devices or browsers. This version supports secure cloud sync through Supabase while retaining local storage as an offline fallback.

## 1. Create a Supabase project

Create a project at Supabase and open its SQL Editor.

## 2. Create the recipes table and security policies

Run this SQL:

```sql
create table public.recipes (
  id uuid primary key,
  user_id uuid not null references auth.users(id) on delete cascade,
  name text not null,
  category text not null,
  last_eaten date not null,
  notes text not null default '',
  updated_at timestamptz not null default now()
);

alter table public.recipes enable row level security;

create policy "Users can read their own recipes"
on public.recipes for select
using ((select auth.uid()) = user_id);

create policy "Users can insert their own recipes"
on public.recipes for insert
with check ((select auth.uid()) = user_id);

create policy "Users can update their own recipes"
on public.recipes for update
using ((select auth.uid()) = user_id)
with check ((select auth.uid()) = user_id);

create policy "Users can delete their own recipes"
on public.recipes for delete
using ((select auth.uid()) = user_id);
```

## 3. Public project values (already configured)

The supplied `index.html` already contains your Project URL and publishable key.

Never paste the `service_role` key into a website.

```js
const CLOUD_CONFIG = {
  supabaseUrl: 'https://kupcdhbexmejlomdelgo.supabase.co',
  supabaseAnonKey: 'sb_publishable_GaShiF3gYPmWx85RdZ1p7w_iZ0Eqq4Q'
};
```

## 4. Configure authentication

In Supabase Authentication settings, enable Email authentication. By default, new accounts may need to confirm their email before the first sign-in.

## 5. Publish to GitHub Pages

Upload `index.html` to your repository and enable GitHub Pages. Use the **Cloud account** button in the app, then sign in with the same email and password in every browser.

Local recipes are merged into the cloud the first time you sign in. The app continues saving locally when offline and synchronizes again when online.
