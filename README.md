# SK BF26 | Página de Captura não alunos — Black Contábil Vitalícia

Página estática. Não precisa de build no deploy: o CSS já está compilado em `black/css/styles.css`.
No ar em https://stephaniekalynka.com/black (o projeto `lp-black-26-alunos`, dono do domínio, faz rewrite de `/black` para este deploy).
Página de base própria, com `noindex` (meta, header `X-Robots-Tag` e `robots.txt`).

## 1. Subir no GitHub

    git init
    git add .
    git commit -m "primeira versão"
    git branch -M main
    git remote add origin https://github.com/<usuario>/<repo>.git
    git push -u origin main

## 2. Publicar na Vercel

1. vercel.com → Add New → Project → importe o repositório.
2. Framework Preset: **Other**. Build Command: deixe vazio. Output Directory: deixe vazio (raiz).
3. Deploy. O domínio `.vercel.app` sai em segundos.
4. Domínio próprio: Settings → Domains → adicione e aponte o DNS conforme a instrução da tela.

## 3. O que trocar e onde

| O quê | Onde |
|---|---|
| Destino do formulário (webhook) | `black/js/main.js`, linha 1: `const ENDPOINT = '...'` |
| Link do Grupo VIP do WhatsApp | `black/js/main.js`, linha 2: `const GRUPO_VIP = '...'` |
| Data e hora da revelação | `black/js/main.js`, linha 3: `const REVELACAO = '2026-11-04T20:00:00-03:00'` |
| Nome e ID do formulário | `black/js/main.js`, `FORM_ID` e `FORM_NOME` (logo abaixo do `DESTINO_SUCESSO`) |
| Destino depois do cadastro | `black/js/main.js`, linha 4: `const DESTINO_SUCESSO = '/black/obrigado'` |
| Destino da página depois da revelação | `black/js/main.js`, função `renderContador`, comentário `location.replace(...)` |
| Rodapé (razão social, CNPJ, política, contato) | `black/index.html`, bloco `<!-- 9-rodape -->` |
| URL da política de privacidade (microcópia) | `black/index.html`, `<a class="form__link" href="#">` nos dois formulários |
| Cores e fontes | `src/input.css`, bloco `:root` (depois recompile) |

O webhook recebe `POST` com `Content-Type: application/json` e precisa aceitar CORS do seu domínio
(responder ao `OPTIONS`). Qualquer resposta fora de 2xx mostra a mensagem de erro no formulário.

Payload enviado:

```json
{
  "email": "aluna@email.com",
  "whatsapp": "5511912345678",
  "form_id": "sk-bf26-captura-nao-alunos",
  "form_nome": "Black Contábil Vitalícia | Captura Não Alunos",
  "form_local": "topo",
  "data": "15/10/2026",
  "hora": "14:32:05",
  "enviado_em": "2026-10-15T17:32:05.000Z",
  "page_url": "https://stephaniekalynka.com/black?utm_source=...",
  "utm_source": "", "utm_medium": "", "utm_campaign": "", "utm_content": "", "utm_term": "", "utm_id": "",
  "fbclid": "", "gclid": "", "gbraid": "", "wbraid": "", "ttclid": "", "msclkid": "", "src": "", "sck": ""
}
```

`data` e `hora` estão no horário de Brasília; `enviado_em` é o mesmo instante em UTC. `form_local` diz
qual dos dois formulários da página foi usado (`topo` ou `final`). `page_url` é a URL inteira da página.

Essas chaves vão sempre (vazias se não vierem). Qualquer outra `utm_*` da URL (ex.: `utm_adset`) entra
também, com o nome em minúsculas. O rastreio fica salvo no navegador por 30 dias: quem chega pelo
anúncio, sai e volta depois pelo link sem UTM ainda é atribuído à campanha. Ele também segue na URL da
página de obrigado (`/black/obrigado?utm_source=...`).

O Meta Pixel não vem instalado. Se você instalar, o evento `Lead` já dispara no sucesso.

## 4. Recompilar o CSS

Depois de mexer em `black/index.html`, `design-system.html` ou `src/input.css`:

    npm install && npm run build

Tudo marcado com `[REVISAR]` ou `[FALTA: ...]` no HTML e no JS precisa da sua conferência antes de anunciar.
