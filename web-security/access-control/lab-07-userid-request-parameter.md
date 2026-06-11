# Lab 07 — User ID Controlled by Request Parameter

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

**Plataforma:** PortSwigger Web Security Academy  
**Categoria:** Access Control / IDOR  
**Dificuldade:** 🟢 Apprentice  
**Data:** 2026-06-08

### Descrição

A página de conta do usuário exibe dados sensíveis — incluindo a API key — e é controlada por um parâmetro de ID na URL. O objetivo é acessar a conta de `carlos` e extrair sua API key. Credenciais disponíveis: `wiener:peter`.

### Conceito

Este é um caso clássico de **IDOR (Insecure Direct Object Reference)**: a aplicação usa um identificador controlável pelo usuário — neste caso, o próprio username na query string — para acessar recursos sem verificar se o solicitante tem permissão sobre aquele objeto. Qualquer usuário autenticado pode acessar os dados de qualquer outro simplesmente alterando o parâmetro na URL.

### Reconhecimento

Após o login com `wiener:peter`, a URL da página de conta era:

```
GET /my-account?id=wiener HTTP/1.1
```

O parâmetro `id` era explicitamente o username — um identificador previsível e diretamente manipulável.

### Exploração

Alterar o parâmetro `id` para o username do alvo foi suficiente:

```
GET /my-account?id=carlos HTTP/1.1
```

O servidor retornou a página de conta de `carlos` com sua API key exposta, sem verificar se o usuário autenticado tinha permissão para acessar aquele perfil.

### Solução

Substituir `id=wiener` por `id=carlos` na URL e capturar a API key exibida na página.

### O que aprendi

- IDOR ocorre quando a aplicação usa identificadores controláveis pelo usuário para acessar objetos sem verificar a propriedade daquele recurso
- Usernames e IDs sequenciais são especialmente perigosos como parâmetros de acesso — são previsíveis e fáceis de enumerar
- A correção exige verificar, server-side, se o usuário autenticado é dono do recurso solicitado

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

The user account page displays sensitive data — including an API key — and is controlled by an ID parameter in the URL. The goal is to access `carlos`'s account and extract his API key. Available credentials: `wiener:peter`.

### Concept

This is a classic **IDOR (Insecure Direct Object Reference)** case: the application uses a user-controllable identifier — in this case, the username in the query string — to access resources without verifying whether the requester has permission over that object. Any authenticated user can access another user's data simply by changing the URL parameter.

### Reconnaissance

After logging in with `wiener:peter`, the account page URL was:

```
GET /my-account?id=wiener HTTP/1.1
```

The `id` parameter was explicitly the username — a predictable and directly manipulable identifier.

### Exploitation

Changing the `id` parameter to the target's username was sufficient:

```
GET /my-account?id=carlos HTTP/1.1
```

The server returned `carlos`'s account page with his API key exposed, without verifying whether the authenticated user had permission to access that profile.

### Solution

Replace `id=wiener` with `id=carlos` in the URL and capture the API key displayed on the page.

### What I learned

- IDOR occurs when the application uses user-controllable identifiers to access objects without verifying ownership of that resource
- Usernames and sequential IDs are especially dangerous as access parameters — they're predictable and easy to enumerate
- The fix requires verifying server-side that the authenticated user owns the requested resource

### References

- [PortSwigger — IDOR](https://portswigger.net/web-security/access-control/idor)
- [OWASP — Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)
