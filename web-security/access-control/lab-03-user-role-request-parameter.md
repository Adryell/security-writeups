# Lab 03 — User Role Controlled by Request Parameter

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

**Plataforma:** PortSwigger Web Security Academy  
**Categoria:** Access Control  
**Dificuldade:** 🟢 Apprentice  
**Data:** 2026-06-08

### Descrição

O painel administrativo em `/admin` identifica administradores através de um cookie controlável pelo cliente. O objetivo é manipular esse cookie para obter acesso ao painel e excluir o usuário `carlos`. Credenciais disponíveis: `wiener:peter`.

### Conceito

Confiar em valores enviados pelo cliente — como cookies — para determinar o papel de um usuário é uma falha grave de design. Cookies são totalmente controláveis pelo usuário e podem ser alterados sem qualquer restrição pelo Burp Suite, DevTools ou qualquer proxy HTTP. A verificação de papel deve sempre ser feita server-side, com base em sessões seguras armazenadas no servidor.

### Reconhecimento

Após realizar o login com `wiener:peter` e interceptar a requisição via Burp Proxy, foi identificado o seguinte cookie na requisição:

```
Cookie: Admin=false
```

O servidor usa esse valor para determinar se o usuário tem acesso ao painel administrativo — um mecanismo completamente controlável pelo cliente.

### Exploração

No Burp Suite, com a requisição interceptada, o cookie foi alterado de:

```
Cookie: Admin=false
```

Para:

```
Cookie: Admin=true
```

Com essa modificação, a requisição para `/admin` foi aceita pelo servidor, que concedeu acesso ao painel sem qualquer verificação adicional.

### Solução

Interceptar a requisição via Burp Proxy, alterar `Admin=false` para `Admin=true` no cookie e acessar `/admin` para excluir o usuário `carlos`.

### O que aprendi

- Nunca confiar em dados enviados pelo cliente para decisões de autorização — cookies, headers e parâmetros são completamente controláveis pelo atacante
- O papel do usuário deve ser determinado server-side, com base no ID de sessão vinculado ao banco de dados
- Interceptar e modificar cookies é trivial com Burp Suite — se a segurança depende de um cookie não assinado, ela não existe

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

The admin panel at `/admin` identifies administrators through a client-controllable cookie. The goal is to manipulate this cookie to gain panel access and delete user `carlos`. Available credentials: `wiener:peter`.

### Concept

Trusting client-sent values — such as cookies — to determine a user's role is a severe design flaw. Cookies are fully controllable by the user and can be modified without restriction using Burp Suite, DevTools, or any HTTP proxy. Role verification must always happen server-side, based on secure sessions stored on the server.

### Reconnaissance

After logging in with `wiener:peter` and intercepting the request via Burp Proxy, the following cookie was identified:

```
Cookie: Admin=false
```

The server uses this value to determine admin panel access — a mechanism completely controlled by the client.

### Exploitation

In Burp Suite, with the request intercepted, the cookie was changed from:

```
Cookie: Admin=false
```

To:

```
Cookie: Admin=true
```

With this modification, the request to `/admin` was accepted by the server, which granted access without any further verification.

### Solution

Intercept the request via Burp Proxy, change `Admin=false` to `Admin=true` in the cookie, then navigate to `/admin` and delete user `carlos`.

### What I learned

- Never trust client-supplied data for authorization decisions — cookies, headers, and parameters are fully attacker-controlled
- User roles must be determined server-side, based on a session ID tied to the database
- Intercepting and modifying cookies is trivial with Burp Suite — if security relies on an unsigned cookie, it doesn't exist

### References

- [PortSwigger — Access Control](https://portswigger.net/web-security/access-control)
- [OWASP — Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)
