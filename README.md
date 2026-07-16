# Landing AI

Sistema que gera landing pages modernas automaticamente a partir dos dados de um negócio (nome, Instagram, WhatsApp, endereço), com galeria, agendamento online e painel administrativo.

## Stack

Next.js 14 (App Router) · React · Tailwind CSS · Framer Motion · Three.js (`@react-three/fiber`) · Supabase (Postgres + Auth)

## Estrutura

```
app/
  page.tsx                 → formulário inicial (cria a landing page)
  [slug]/page.tsx           → landing page pública gerada (ex: /studio-bella-xxxx)
  admin/page.tsx            → painel administrativo (fotos, textos, serviços, horários, agendamentos)
  admin/login/page.tsx       → login do admin (Supabase Auth)
  api/generate-landing/      → gera o copy com IA e cria o registro do cliente
  api/instagram/              → busca fotos via Instagram Graph API
  api/bookings/                → cria agendamentos com checagem de conflito
components/                  → Hero, Gallery, Services, About, Testimonials, LocationMap,
                                BookingForm, WhatsAppButton, Footer, Scene3D (decoração 3D)
lib/                         → supabase client, tipos, variantes de animação, hook de parallax
supabase/schema.sql          → schema completo com RLS
```

## Como rodar

1. Instale as dependências:
   ```bash
   npm install
   ```

2. Crie um projeto em [supabase.com](https://supabase.com), abra o **SQL Editor** e rode todo o conteúdo de `supabase/schema.sql`.

3. Copie `.env.example` para `.env.local` e preencha:
   - `NEXT_PUBLIC_SUPABASE_URL` / `NEXT_PUBLIC_SUPABASE_ANON_KEY` / `SUPABASE_SERVICE_ROLE_KEY` — em *Project Settings → API* no Supabase.
   - `ANTHROPIC_API_KEY` — usada para gerar o título, subtítulo, texto "sobre" e lista de serviços automaticamente.

4. Rode o projeto:
   ```bash
   npm run dev
   ```

5. Acesse `http://localhost:3000`, preencha o formulário → a IA gera a landing page e você é redirecionado ao admin.

## Criando o primeiro usuário admin

O painel usa Supabase Auth. Depois de criar um cliente (passo 5 acima):

1. Em **Authentication → Users** no Supabase, crie um usuário (email/senha).
2. No **SQL Editor**, vincule esse usuário ao cliente criado:
   ```sql
   insert into admin_users (id, client_id, full_name)
   values ('uuid-do-usuario-criado', 'uuid-do-client', 'Nome do Admin');
   ```
3. Acesse `/admin/login` e entre com esse email/senha.

## Sobre a integração com Instagram

O Instagram **não permite raspagem (scraping)** de perfis — isso viola os Termos de Uso da Meta. Este projeto usa a **Instagram Graph API oficial**, que exige:

- Conta Instagram Business ou Creator vinculada a uma Página do Facebook
- Um app no [Meta for Developers](https://developers.facebook.com) com permissão `instagram_basic`
- Um access token de longa duração

Se o cliente não tiver isso configurado, **não tem problema**: o admin pode simplesmente colar URLs de fotos manualmente na aba "Fotos" do painel — a galeria funciona normalmente.

## Publicando uma landing page

Por padrão toda landing page nasce como rascunho (`is_published = false`). No painel admin, clique no botão "Rascunho" no topo para publicá-la — só então ela fica acessível publicamente em `/seu-slug`.

## Próximos passos sugeridos

- Upload de imagens direto para o Supabase Storage (hoje é feito por URL)
- Notificação por WhatsApp/e-mail ao admin quando um novo agendamento chega
- Multi-idioma
- Editor de cor da marca (`brand_color`) direto no painel
