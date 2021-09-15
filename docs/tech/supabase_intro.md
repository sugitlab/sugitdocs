# Supabase 入門中

[Supabase Docs](https://supabase.io/docs) を読んで勉強中

- API Gateway として Kong を使用
- Auth は GoTrue、 rest は PostgRESTなど、OSSで構成している
- Firestoreのような NOSQLではなく　Postgres　を採用

##  Database

- Postgres を使用。スケーラブルなRDB。
- WebUIが充実しているのでSQLに不慣れでも始めやすい
- RDBのリレーションも確認しやすい
- WebUIのSQLエディタがある。作成したクエリはWebで保存できる。再利用可能。
- 自前の [Realtime Server](https://github.com/supabase/realtime) を使ってリアルタイム機能を提供している
- 全てにおいて Postgres。ユーザー情報も同様。
- postgres 用の [extension](https://supabase.io/docs/guides/database/extensions) がつかえる　

## Auth

- OAuthたっぷり
    - Apple, Google, Discord, Facebook, Twitter, GitHub, GitLab, BitBucket, ...
- 認証情報は　　postgres に入る
- 認証情報を用いたアクセス制限については Postgresの [RowLevelSecurity](https://www.postgresql.org/docs/current/ddl-rowsecurity.html)に準拠する

例えば以下のようなクエリを組めば、 profiles テーブルに対して変更 (update) を実行できるのは auth.uid が一致する場合に限ることができる。

```sql
-- 1. Create table
create table profiles (
  id uuid references auth.users,
  avatar_url text
);

-- 2. Enable RLS
alter table profiles
  enable row level security;

-- 3. Create Policy
create policy "Users can update their own profiles."
  on profiles for update using (
    auth.uid() = id
  );
```

以下のように経過時間で制限をかけることもできる

```sql
-- 1. Create table
create table if not exists stories (
  id uuid not null primary key DEFAULT uuid_generate_v4(),
  created_at timestamp with time zone default timezone('utc' :: text, now()) not null,
  content text not null
);

-- 2. Enable RLS
alter table stories
  enable row level security;

-- 3. Create Policy
create policy "Stories are live for a day"
  on stories
  for select using (
    created_at > (current_timestamp - interval '1 day')
  );
```

## Storage

File / Folder / Bucket という三層構造
- File: 保存するファイルそのもの
- Folder: ファイルをわかりやすく分類するためのもの
- Bucket: Folderの上位概念。Bucketに対してアクセス制限をかけたりする。

- Bucketはクライアント側から作成できる（JS, Dart)
- セキュリティルールは SQL か WebUI で定義する

こんな感じ。Storageも結局はpostgreなので、RLSでOK。

```sql
-- 1. Allow logged-in access to any files in the "restricted" bucket
create policy "Restricted Access"
on storage.objects for select
using (
  bucket_id = 'restricted'
  and auth.role() = 'authenticated'
);
```

## API

Postgres のクエリを直接[API](https://supabase.io/docs/guides/api)にして生やして使える

