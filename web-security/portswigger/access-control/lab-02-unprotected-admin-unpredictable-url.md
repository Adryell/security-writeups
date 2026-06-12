# Lab 02 — Unprotected Admin Functionality with Unpredictable URL

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

**Plataforma:** PortSwigger Web Security Academy  
**Categoria:** Access Control  
**Dificuldade:** 🟢 Apprentice  
**Data:** 2026-06-08

### Descrição

O painel administrativo está em uma URL "imprevisível" — sem referência óbvia no robots.txt ou na navegação comum. Ainda assim, sua localização é vazada em algum ponto da aplicação. O objetivo é encontrá-lo e excluir o usuário `carlos`.

### Conceito

Gerar URLs aleatórias para painéis administrativos é uma tentativa de obscurecer o acesso, mas não resolve o problema central: se o cliente precisar saber a URL para renderizar o menu de admin, ela estará exposta no código-fonte da página. Qualquer usuário com acesso ao HTML consegue extraí-la — independentemente de estar autenticado como administrador ou não.

### Reconhecimento

Ao inspecionar o código-fonte da página de login (`Elements` no DevTools), foi encontrado um script de renderização condicional do menu de administrador:

```js
var isAdmin = false;
if (isAdmin) {
    var topLinksTag = document.getElementsByClassName("top-links")[0];
    var adminPanelTag = document.createElement('a');
    adminPanelTag.setAttribute('href', '/admin-8x4kne');
    adminPanelTag.innerText = 'Admin panel';
    topLinksTag.append(adminPanelTag);
}
```

Mesmo com `isAdmin = false` — impedindo a exibição do link na interface — o caminho `/admin-8x4kne` está hardcoded no JavaScript e visível a qualquer pessoa que inspecione o código.

### Exploração

Com o caminho extraído do fonte, o acesso foi direto:

```
GET /admin-8x4kne HTTP/1.1
```

Nenhuma verificação de autenticação no servidor. O painel carregou normalmente.

### Solução

Extrair o caminho do painel a partir do JavaScript no código-fonte, acessar `/admin-8x4kne` e excluir o usuário `carlos`.

### O que aprendi

- Lógica de autorização nunca deve depender de variáveis client-side como `isAdmin` — elas são facilmente manipuláveis e inspecionáveis
- URLs "secretas" no código JavaScript são públicas: qualquer pessoa com DevTools tem acesso
- A verificação de permissão deve sempre ocorrer no servidor, não no cliente
- Inspecionar o fonte da página é um passo obrigatório no reconhecimento de aplicações web

### Referências

- [PortSwigger — Access Control](https://portswigger.net/web-security/access-control)
- [OWASP — Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)

---

## 🇺🇸 English

**Platform:** PortSwigger Web Security Academy  
**Category:** Access Control  
**Difficulty:** 🟢 Apprentice  
**Date:** 2026-06-08

### Description

The admin panel sits at an "unpredictable" URL — no obvious reference in robots.txt or common navigation. Even so, its location is leaked somewhere in the application. The goal is to find it and delete user `carlos`.

### Concept

Generating random URLs for admin panels is an attempt to obscure access, but it doesn't solve the core problem: if the client needs to know the URL to render the admin menu, it will be exposed in the page's source code. Any user with access to the HTML can extract it — regardless of whether they're authenticated as an admin or not.

### Reconnaissance

Inspecting the login page's source code (`Elements` in DevTools) revealed a conditional rendering script for the admin menu:

```js
var isAdmin = false;
if (isAdmin) {
    var topLinksTag = document.getElementsByClassName("top-links")[0];
    var adminPanelTag = document.createElement('a');
    adminPanelTag.setAttribute('href', '/admin-8x4kne');
    adminPanelTag.innerText = 'Admin panel';
    topLinksTag.append(adminPanelTag);
}
```

Even with `isAdmin = false` — preventing the link from appearing in the UI — the path `/admin-8x4kne` is hardcoded in the JavaScript and visible to anyone who inspects the source.

### Exploitation

With the path extracted from the source, access was straightforward:

```
GET /admin-8x4kne HTTP/1.1
```

No server-side authentication check. The panel loaded normally.

### Solution

Extract the panel path from the JavaScript source code, navigate to `/admin-8x4kne`, and delete user `carlos`.

### What I learned

- Authorization logic should never rely on client-side variables like `isAdmin` — they're easily inspectable and manipulable
- "Secret" URLs in JavaScript are public: anyone with DevTools has access
- Permission checks must always happen server-side, never client-side
- Inspecting page source is a mandatory step in web application reconnaissance

### References

- [PortSwigger — Access Control](https://portswigger.net/web-security/access-control)
- [OWASP — Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)
