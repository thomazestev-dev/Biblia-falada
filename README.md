# Bíblia em Áudio — Área de Membros

Fluxo: **Compra → Webhook (Vercel) → Supabase cria o acesso e envia e-mail → membro define a senha → ouve os áudios com progresso salvo.**

## 1. Supabase
1. Crie um projeto em supabase.com.
2. SQL Editor → cole `supabase/schema.sql` → Run.
3. Storage → bucket `audio` (já criado pelo SQL). Envie os arquivos assim:
   - `salmos/1.mp3`, `salmos/2.mp3`… (nome do livro sem acento, minúsculo, espaços viram `-`; ex.: `1-corintios/3.mp3`, `jo/1.mp3`, `joao/1.mp3`)
   - `bonus/boas-vindas.mp3`, `bonus/plano-30-dias.pdf`, `bonus/habito-de-ouvir.pdf`
4. Authentication → URL Configuration: em **Site URL** coloque a URL da Vercel e adicione-a em **Redirect URLs**.
5. Authentication → Email Templates: traduza "Invite user" e "Reset password" para português.
6. Settings → API: copie `Project URL`, a chave `anon` e a chave `service_role`.

## 2. GitHub
Edite `config.js` com a URL e a chave **anon**, depois:
```
git init && git add . && git commit -m "primeira versão"
git remote add origin https://github.com/SEU-USUARIO/biblia-audio.git
git push -u origin main
```

## 3. Vercel
1. Add New Project → importe o repositório (sem framework, deixe tudo padrão).
2. Settings → Environment Variables: `SUPABASE_URL`, `SUPABASE_SERVICE_ROLE_KEY`, `WEBHOOK_SECRET`, `SITE_URL` (veja `.env.example`). Faça Redeploy.
3. A cada `git push`, a Vercel publica sozinha.

## 4. Plataforma de vendas
Cadastre um webhook de compra aprovada **e** de reembolso/chargeback apontando para:
`https://SEU-SITE.vercel.app/api/webhook?secret=SEU_WEBHOOK_SECRET`

Teste sem comprar:
```
curl -X POST "https://SEU-SITE.vercel.app/api/webhook?secret=SEU_WEBHOOK_SECRET" \
 -H "Content-Type: application/json" \
 -d '{"email":"voce@email.com","name":"Seu Nome","status":"approved"}'
```

## Segurança
- A chave `service_role` fica só na Vercel, nunca no GitHub.
- Os áudios são privados: só membros com `active = true` recebem links temporários (1 hora).
- Reembolso → `active = false` → acesso bloqueado na hora.
