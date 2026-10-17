# Cardápio Mi vê um brownie — publicação e painel da vendedora

## O que tem na pasta
- `index.html` — cardápio dos clientes (link público)
- `vendedora.html` — painel da vendedora (editar produtos, fotos, preços, anúncios, retirada)
- `loja-config.js` — onde você cola os dados do Supabase (passo 5)

## Como funciona
O painel salva o cardápio num banco gratuito (Supabase). O cardápio dos clientes lê esse banco a cada acesso. Só quem entra com e-mail e senha no painel consegue alterar.

## Passo a passo (cerca de 15 minutos, melhor no computador)

1. Crie uma conta grátis em **supabase.com** e clique em **New project** (escolha a região São Paulo).
2. No menu **SQL Editor**, cole o código abaixo e clique em **Run**:

```sql
create table if not exists cardapio (
  id int primary key,
  dados jsonb not null,
  atualizado timestamptz not null default now()
);
alter table cardapio enable row level security;
create policy "clientes leem o cardapio" on cardapio for select using (true);
create policy "vendedora cria" on cardapio for insert to authenticated with check (true);
create policy "vendedora altera" on cardapio for update to authenticated using (true) with check (true);
```

3. Em **Authentication → Users → Add user**, crie o usuário da vendedora (e-mail + senha forte) e marque **Auto Confirm User**.
4. Em **Authentication → Sign In / Providers**, **desligue "Allow new users to sign up"**. Isso é importante: impede que qualquer pessoa crie conta e altere o cardápio.
5. Em **Project Settings → API**, copie **Project URL** e a chave **anon public**. Cole os dois em `loja-config.js`:
   ```js
   window.LOJA = { url: "https://SEU-PROJETO.supabase.co", anonKey: "SUA-CHAVE-ANON" };
   ```
6. Publique a **pasta inteira** em **app.netlify.com/drop** (arraste a pasta). O Netlify gera o link do site.

## Links
- Clientes: `https://SEU-SITE.netlify.app/`
- Vendedora: `https://SEU-SITE.netlify.app/vendedora.html`

Na primeira vez que a vendedora entrar e tocar em **Salvar e publicar**, o cardápio atual é gravado no banco. A partir daí, as mudanças aparecem para os clientes ao recarregar a página.

## Usando o painel
- **Produtos:** Editar (nome, descrição, preço, foto, disponível), Subir/Descer, Excluir, **+ Novo produto** (começa oculto até você marcar "Disponível").
- **Anúncios:** até 5; cada um com título, texto, produto do botão, foto e preço promocional (opcional).
- **Retirada:** endereço, link do mapa, prazo e horários.
- **Marca:** nome e subtítulo.
- Nada vai ao ar até tocar em **Salvar e publicar**.

## Segurança
- A chave `anon` é pública por natureza; quem protege a escrita é o login (regras do passo 2).
- O link `/vendedora.html` não é secreto, mas só funciona com e-mail e senha. Use senha forte.
- O número de WhatsApp dos pedidos fica fixo no código (`CONFIG.whatsapp` em `index.html`) e não é editável pelo painel.

## Modo demonstração
Com `loja-config.js` vazio, o painel salva só no navegador (não publica para os clientes). Serve para experimentar.

## Observações
- As fotos enviadas são reduzidas (480 px) e guardadas dentro dos dados do cardápio.
- Dentro do app do Claude o botão do WhatsApp pode ser bloqueado; no site hospedado, abra no navegador do celular.
- Testado com um servidor simulado do Supabase, não com um projeto Supabase real.
