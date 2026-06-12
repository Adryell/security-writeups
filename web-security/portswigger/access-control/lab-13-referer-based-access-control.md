# Lab 13 — Referer-Based Access Control

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

**Plataforma:** PortSwigger Web Security Academy  
**Categoria:** Access Control  
**Dificuldade:** 🟡 Practitioner  
**Data:** 2026-06-08

### Descrição

O endpoint `/admin-roles` usa o header `Referer` para verificar se a requisição veio do painel administrativo. O objetivo é forjar esse header para promover `wiener` a administrador sem ter acesso legítimo ao painel. Credenciais de admin para reconhecimento: `administrator:admin`. Credenciais do alvo: `wiener:peter`.

### Conceito

O header `Referer` indica a página de origem de uma requisição — e é completamente controlável pelo cliente. Usá-lo como mecanismo de controle de acesso é uma falha grave: qualquer atacante com um proxy HTTP pode forjar seu valor e simular que a requisição veio de qualquer origem desejada. Controle de acesso baseado em `Referer` oferece zero proteção real.

### Reconhecimento

Logado como `administrator`, a promoção de um usuário foi executada e capturada via Burp Proxy. A requisição revelou dois elementos críticos:

**Endpoint e parâmetros:**
```
GET /admin-roles?username=carlos&action=upgrade HTTP/1.1
```

**Header Referer:**
```
Referer: https://TARGET.web-security-academy.net/admin
```

O servidor verificava se a requisição havia sido originada em `/admin` — mas essa verificação era feita apenas com base no header `Referer`, que o cliente controla.

### Exploração

No Burp Repeater, logado como `wiener`:

1. Cookie de sessão do `administrator` substituído pelo de `wiener`
2. `username=carlos` alterado para `username=wiener`
3. Header `Referer` mantido apontando para `/admin`

```
GET /admin-roles?username=wiener&action=upgrade HTTP/1.1
Cookie: session=<cookie_do_wiener>
Referer: https://TARGET.web-security-academy.net/admin
```

O servidor validou o `Referer` e processou a requisição, promovendo `wiener` a administrador.

### Solução

Forjar o header `Referer` apontando para `/admin` na requisição de promoção, com o cookie de sessão de `wiener` e `username=wiener`.

### O que aprendi

- O header `Referer` é completamente controlável pelo cliente — nunca deve ser usado para decisões de autorização
- Headers HTTP podem ser arbitrariamente definidos ou modificados via Burp Suite, curl ou qualquer cliente HTTP
- Controle de acesso deve ser baseado em identidade autenticada (sessão + papel no servidor), não em metadados da requisição como `Referer` ou `Origin`

### Referências

- [PortSwigger — Access Control](https://portswigger.net/web-security/access-control)
- [OWASP — Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)

---

## 🇺🇸 English

**Platform:** PortSwigger Web Security Academy  
**Category:** Access Control  
**Difficulty:** 🟡 Practitioner  
**Date:** 2026-06-08

### Description

The `/admin-roles` endpoint uses the `Referer` header to verify whether the request came from the admin panel. The goal is to forge this header to promote `wiener` to admin without legitimate panel access. Admin credentials for reconnaissance: `administrator:admin`. Target credentials: `wiener:peter`.

### Concept

The `Referer` header indicates the origin page of a request — and is completely client-controllable. Using it as an access control mechanism is a severe flaw: any attacker with an HTTP proxy can forge its value and simulate the request coming from any desired origin. `Referer`-based access control offers zero real protection.

### Reconnaissance

Logged in as `administrator`, user promotion was executed and captured via Burp Proxy. The request revealed two critical elements:

**Endpoint and parameters:**
```
GET /admin-roles?username=carlos&action=upgrade HTTP/1.1
```

**Referer header:**
```
Referer: https://TARGET.web-security-academy.net/admin
```

The server verified whether the request originated from `/admin` — but this check was based solely on the `Referer` header, which the client controls.

### Exploitation

In Burp Repeater, logged in as `wiener`:

1. `administrator`'s session cookie replaced with `wiener`'s
2. `username=carlos` changed to `username=wiener`
3. `Referer` header kept pointing to `/admin`

```
GET /admin-roles?username=wiener&action=upgrade HTTP/1.1
Cookie: session=<wiener_session_cookie>
Referer: https://TARGET.web-security-academy.net/admin
```

The server validated the `Referer` and processed the request, promoting `wiener` to admin.

### Solution

Forge the `Referer` header pointing to `/admin` in the promotion request, with `wiener`'s session cookie and `username=wiener`.

### What I learned

- The `Referer` header is completely client-controllable — it should never be used for authorization decisions
- HTTP headers can be arbitrarily set or modified via Burp Suite, curl, or any HTTP client
- Access control must be based on authenticated identity (server-side session + role), not request metadata like `Referer` or `Origin`

### References

- [PortSwigger — Access Control](https://portswigger.net/web-security/access-control)
- [OWASP — Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)
