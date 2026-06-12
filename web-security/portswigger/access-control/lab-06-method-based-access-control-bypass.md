# Lab 06 — Method-Based Access Control Can Be Circumvented

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

**Plataforma:** PortSwigger Web Security Academy  
**Categoria:** Access Control  
**Dificuldade:** 🟡 Practitioner  
**Data:** 2026-06-08

### Descrição

A aplicação implementa controle de acesso baseado, em parte, no método HTTP da requisição. O objetivo é explorar essa falha para promover o usuário `wiener` a administrador. Credenciais de admin disponíveis para reconhecimento: `administrator:admin`. Credenciais do alvo: `wiener:peter`.

### Conceito

Quando uma aplicação aplica regras de autorização apenas para métodos HTTP específicos (como `POST`), mas não para outros (como `GET` ou métodos arbitrários), abre-se uma brecha de bypass. Um atacante pode enviar a mesma requisição usando um método diferente e contornar completamente o controle de acesso — especialmente quando frameworks movem parâmetros do corpo para a query string ao converter `POST` para `GET`.

### Reconhecimento

**Mapeando o fluxo legítimo:**

Logado como `administrator`, foi capturada a requisição de promoção de usuário via Burp Proxy. A ação era executada via `POST`:

```
POST /admin-roles HTTP/1.1
username=carlos&action=upgrade
```

**Testando o bloqueio:**

A mesma requisição `POST` foi enviada com o cookie de sessão substituído pelo de `wiener`. O servidor retornou `401 Unauthorized` — o controle de acesso para `POST` funcionou.

**Fuzzing de métodos:**

O método `POST` foi substituído por `POSTX` (método inválido). A resposta mudou de `401 Unauthorized` para `400 Bad Request — missing parameter`. Isso revelou que a barreira de autorização foi ultrapassada: a requisição chegou à lógica interna da aplicação, que reclamou de parâmetro faltante — não de acesso negado.

### Exploração

Com a confirmação de que apenas `POST` era bloqueado, a requisição foi convertida para `GET` no Burp Suite (os parâmetros migraram do corpo para a query string). O username foi alterado para `wiener` e o cookie de sessão substituído pelo do usuário comum:

```
GET /admin-roles?username=wiener&action=upgrade HTTP/1.1
Cookie: session=<cookie_do_wiener>
```

O servidor processou a requisição normalmente e promoveu `wiener` a administrador.

### Solução

Converter a requisição de `POST` para `GET`, substituir o cookie de sessão pelo de `wiener` e alterar o `username` para `wiener` na query string.

### O que aprendi

- Controles de acesso devem ser aplicados a todos os métodos HTTP, não apenas ao método "esperado"
- Métodos HTTP alternativos (`GET`, `PUT`, `PATCH`, métodos inválidos) podem ser usados para bypassar regras que só filtram `POST`
- A mudança na resposta — de `401` para `400` — foi o sinal de que o bloqueio havia sido contornado
- Burp Suite permite converter métodos facilmente via "Change request method"

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

The application implements access control based partly on the HTTP request method. The goal is to exploit this flaw to promote user `wiener` to admin. Admin credentials available for reconnaissance: `administrator:admin`. Target credentials: `wiener:peter`.

### Concept

When an application enforces authorization rules only for specific HTTP methods (like `POST`) but not others (like `GET` or arbitrary methods), a bypass opportunity arises. An attacker can send the same request using a different method and completely circumvent access control — especially when frameworks move parameters from the body to the query string when converting `POST` to `GET`.

### Reconnaissance

**Mapping the legitimate flow:**

Logged in as `administrator`, the user promotion request was captured via Burp Proxy. The action was executed via `POST`:

```
POST /admin-roles HTTP/1.1
username=carlos&action=upgrade
```

**Testing the block:**

The same `POST` request was sent with the session cookie replaced by `wiener`'s. The server returned `401 Unauthorized` — the access control for `POST` worked.

**Method fuzzing:**

The `POST` method was replaced with `POSTX` (invalid method). The response changed from `401 Unauthorized` to `400 Bad Request — missing parameter`. This revealed the authorization barrier was bypassed: the request reached the application's internal logic, which complained about a missing parameter — not about denied access.

### Exploitation

With confirmation that only `POST` was blocked, the request was converted to `GET` in Burp Suite (parameters migrated from the body to the query string). The username was changed to `wiener` and the session cookie replaced with the regular user's:

```
GET /admin-roles?username=wiener&action=upgrade HTTP/1.1
Cookie: session=<wiener_session_cookie>
```

The server processed the request normally and promoted `wiener` to admin.

### Solution

Convert the request from `POST` to `GET`, replace the session cookie with `wiener`'s, and change the `username` to `wiener` in the query string.

### What I learned

- Access controls must be applied to all HTTP methods, not just the "expected" one
- Alternative HTTP methods (`GET`, `PUT`, `PATCH`, invalid methods) can bypass rules that only filter `POST`
- The response change — from `401` to `400` — was the signal that the block had been bypassed
- Burp Suite allows easy method conversion via "Change request method"

### References

- [PortSwigger — Access Control](https://portswigger.net/web-security/access-control)
- [OWASP — Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)
