# Lab 09 — User ID Controlled by Request Parameter with Data Leakage in Redirect

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

**Plataforma:** PortSwigger Web Security Academy  
**Categoria:** Access Control / IDOR  
**Dificuldade:** 🟢 Apprentice  
**Data:** 2026-06-08

### Descrição

Ao tentar acessar a conta de outro usuário, a aplicação redireciona para a página de login — mas renderiza a página do perfil alvo brevemente antes do redirecionamento acontecer. Esse conteúdo, invisível no browser, é capturável via proxy. O objetivo é extrair a API key de `carlos`. Credenciais disponíveis: `wiener:peter`.

### Conceito

Redirects de segurança são comuns para proteger recursos não autorizados, mas a falha aqui está em **quando** o redirecionamento ocorre: o servidor processa e renderiza a página do recurso protegido primeiro, e só então emite o redirect. O corpo da resposta HTTP 302 contém o HTML completo do perfil alvo — incluindo dados sensíveis. No browser, o usuário vê apenas o redirecionamento; no proxy, todo o conteúdo da resposta está exposto.

### Reconhecimento

Ao alterar `id=wiener` para `id=carlos` na URL, o browser redirecionou imediatamente para a página de login, sem exibir nada. Aparentemente protegido.

### Exploração

Com o Burp Proxy ativo e a intercepção habilitada, a requisição foi capturada:

```
GET /my-account?id=carlos HTTP/1.1
```

A resposta do servidor foi um `302 Found` — mas o corpo da resposta continha o HTML completo da página de conta de `carlos`, incluindo sua API key. O redirect instrui o browser a ignorar o corpo, mas o Burp captura tudo.

### Solução

Interceptar a resposta da requisição `GET /my-account?id=carlos` via Burp Proxy e extrair a API key do corpo HTML da resposta 302.

### O que aprendi

- Um redirect não apaga o corpo da resposta HTTP — o conteúdo ainda é enviado pelo servidor e capturável via proxy
- A lógica correta seria verificar a autorização **antes** de processar e renderizar o recurso, emitindo o redirect sem gerar o conteúdo protegido
- Sempre analisar respostas de redirect no Burp — o que o browser ignora pode conter dados críticos

### Referências

- [PortSwigger — IDOR](https://portswigger.net/web-security/access-control/idor)
- [OWASP — Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)

---

## 🇺🇸 English

**Platform:** PortSwigger Web Security Academy  
**Category:** Access Control / IDOR  
**Difficulty:** 🟢 Apprentice  
**Date:** 2026-06-08

### Description

When attempting to access another user's account, the application redirects to the login page — but briefly renders the target's profile before the redirect occurs. This content, invisible in the browser, is capturable via proxy. The goal is to extract `carlos`'s API key. Available credentials: `wiener:peter`.

### Concept

Security redirects are common for protecting unauthorized resources, but the flaw here lies in **when** the redirect occurs: the server processes and renders the protected resource's page first, then issues the redirect. The HTTP 302 response body contains the full HTML of the target profile — including sensitive data. In the browser, the user only sees the redirect; in the proxy, the full response content is exposed.

### Reconnaissance

Changing `id=wiener` to `id=carlos` in the URL caused the browser to immediately redirect to the login page, displaying nothing. Seemingly protected.

### Exploitation

With Burp Proxy active and interception enabled, the request was captured:

```
GET /my-account?id=carlos HTTP/1.1
```

The server response was a `302 Found` — but the response body contained the full HTML of `carlos`'s account page, including his API key. The redirect instructs the browser to ignore the body, but Burp captures everything.

### Solution

Intercept the response to `GET /my-account?id=carlos` via Burp Proxy and extract the API key from the 302 response's HTML body.

### What I learned

- A redirect doesn't erase the HTTP response body — the content is still sent by the server and capturable via proxy
- The correct logic would verify authorization **before** processing and rendering the resource, issuing the redirect without generating the protected content
- Always analyze redirect responses in Burp — what the browser ignores may contain critical data

### References

- [PortSwigger — IDOR](https://portswigger.net/web-security/access-control/idor)
- [OWASP — Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)
