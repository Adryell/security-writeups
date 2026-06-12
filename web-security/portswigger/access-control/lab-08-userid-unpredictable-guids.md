# Lab 08 — User ID Controlled by Request Parameter with Unpredictable User IDs

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

**Plataforma:** PortSwigger Web Security Academy  
**Categoria:** Access Control / IDOR  
**Dificuldade:** 🟢 Apprentice  
**Data:** 2026-06-08

### Descrição

A página de conta usa GUIDs como identificadores, tornando a enumeração direta inviável. Ainda assim, o GUID de `carlos` está exposto em outro ponto da aplicação. O objetivo é encontrá-lo e extrair a API key do usuário. Credenciais disponíveis: `wiener:peter`.

### Conceito

GUIDs (Globally Unique Identifiers) são frequentemente usados como medida de segurança contra IDOR, pois são difíceis de adivinhar ou enumerar. No entanto, se a aplicação vaza esses identificadores em outros contextos — como em posts públicos, comentários ou qualquer área acessível sem autenticação — a proteção se torna inútil. O atacante não precisa adivinhar o GUID; ele apenas precisa encontrá-lo onde foi exposto inadvertidamente.

### Reconhecimento

A URL da página de conta usava um GUID:

```
GET /my-account?id=14dcf67f-95a3-4aa2-9a65-5d9003a15713 HTTP/1.1
```

A tentativa de enumeração direta seria inviável. Porém, ao navegar pelos posts públicos do blog e inspecionar o código-fonte, foi encontrado um link com o GUID de `carlos` exposto em um atributo `href`:

```html
<a href="/blogs?userId=c96bc2b7-0706-4d36-9c2a-203a114ec2e1">carlos</a>
```

### Exploração

Com o GUID de `carlos` em mãos, o parâmetro da URL de conta foi substituído:

```
GET /my-account?id=c96bc2b7-0706-4d36-9c2a-203a114ec2e1 HTTP/1.1
```

O servidor retornou a conta de `carlos` com a API key exposta.

### Solução

Encontrar o GUID de `carlos` no HTML dos posts públicos e usá-lo para acessar `/my-account?id=<guid>`.

### O que aprendi

- GUIDs protegem contra adivinhação, mas não contra vazamento — se o identificador aparece em qualquer lugar público, a proteção cai por terra
- É necessário auditar todos os pontos da aplicação onde IDs de usuário são expostos: posts, comentários, feeds, respostas de API
- A defesa correta combina identificadores não previsíveis **com** verificação de propriedade server-side

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

The account page uses GUIDs as identifiers, making direct enumeration unfeasible. Even so, `carlos`'s GUID is exposed elsewhere in the application. The goal is to find it and extract the user's API key. Available credentials: `wiener:peter`.

### Concept

GUIDs (Globally Unique Identifiers) are frequently used as a security measure against IDOR, since they're hard to guess or enumerate. However, if the application leaks these identifiers in other contexts — such as public posts, comments, or any unauthenticated area — the protection becomes useless. The attacker doesn't need to guess the GUID; they just need to find it where it was inadvertently exposed.

### Reconnaissance

The account page URL used a GUID:

```
GET /my-account?id=14dcf67f-95a3-4aa2-9a65-5d9003a15713 HTTP/1.1
```

Direct enumeration would be unfeasible. However, browsing the blog's public posts and inspecting the source code revealed a link with `carlos`'s GUID exposed in an `href` attribute:

```html
<a href="/blogs?userId=c96bc2b7-0706-4d36-9c2a-203a114ec2e1">carlos</a>
```

### Exploitation

With `carlos`'s GUID in hand, the account URL parameter was replaced:

```
GET /my-account?id=c96bc2b7-0706-4d36-9c2a-203a114ec2e1 HTTP/1.1
```

The server returned `carlos`'s account with the API key exposed.

### Solution

Find `carlos`'s GUID in the public blog posts' HTML and use it to access `/my-account?id=<guid>`.

### What I learned

- GUIDs protect against guessing, but not against leakage — if the identifier appears anywhere public, the protection collapses
- All application points where user IDs are exposed must be audited: posts, comments, feeds, API responses
- The correct defense combines unpredictable identifiers **with** server-side ownership verification

### References

- [PortSwigger — IDOR](https://portswigger.net/web-security/access-control/idor)
- [OWASP — Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)
