# Saldo de Combustível — Atlântico Transportes

Site estático (`index.html`, um arquivo só) para conciliação do saldo de combustível
(Suprimento × Abastecimento × Saldo físico) por unidade/tanque. Mesmo protótipo que
roda como Claude Artifact, adaptado para rodar sozinho no GitHub Pages, com o banco
de dados em um projeto Supabase dedicado.

## Como publicar (GitHub Pages)

1. Suba este repositório no GitHub (branch `main`, arquivo `index.html` na raiz).
2. Em **Settings → Pages**, escolha **Deploy from a branch**, branch `main`, pasta `/ (root)`.
3. O site fica em `https://<usuario>.github.io/<repo>/` (alguns minutos após o primeiro deploy).

## Banco de dados (Supabase)

- Projeto: **saldo-combustivel** (org `fpnetz's Org`, região `sa-east-1`).
- Tabela `public.store`: chave-valor genérica (`path` texto / `data` jsonb), usada como
  `unidades/<id>`, `lancamentos/<id>`, `usuarios/<id>`, `agendamentos/<id>` — o mesmo
  formato de "documento por caminho" que o app já usava no banco do Artifact, então o
  código principal do app não precisou ser reescrito, só a camada de conexão.
- Bucket de Storage `anexos` (público) para os anexos de lançamento (print do sistema,
  foto da régua).
- RLS liberado para a role `anon` (select/insert/update/delete) — é uma ferramenta
  interna sem backend próprio, o mesmo modelo usado no site-suporte-atlantico. O login
  do app (usuário/senha) continua sendo só uma trava de interface, não autenticação
  real do banco — não é cofre de credenciais sensíveis.
- As credenciais (URL do projeto + chave anônima/publicável) já estão embutidas no
  `<script>` de conexão no topo do `index.html` — é assim mesmo, chave anônima do
  Supabase é feita para ir no código do cliente.

## Diferença em relação à versão Claude Artifact

A versão publicada como Claude Artifact usa o banco embutido do próprio Artifact
(`window.claude.use("db"/"assets")`). Este `index.html` inclui, antes do script
principal, uma pequena camada (`shim`) que implementa a mesma interface
(`doc/collection/get/set/update/delete`, `upload`) só que conversando com o Supabase —
então, se este arquivo um dia for aberto dentro de um Claude Artifact, o shim se
desliga sozinho e o Artifact volta a usar o banco dele.

Um efeito colateral bom dessa troca: no Artifact, o anexo (print/foto) era enviado mas
nunca aparecia de volta na tela (bug antigo, sem conserto até agora). Nesta versão, o
`upload` já devolve a URL pública do Storage, então os anexos aparecem normalmente.

## Depois de publicar

Dentro do site, aba **Cadastro** (como admin) tem o botão **"Testar conexão com o
banco"** — use ele para confirmar que o site publicado está realmente gravando e lendo
do Supabase antes de começar a cadastrar dados de verdade.
