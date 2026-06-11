# Lab 12 — Multi-Step Process with No Access Control on One Step

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

**Plataforma:** PortSwigger Web Security Academy  
**Categoria:** Access Control  
**Dificuldade:** 🟡 Practitioner  
**Data:** 2026-06-08

### Descrição

O painel administrativo usa um fluxo de duas etapas para promover usuários: seleção e confirmação. O controle de acesso foi aplicado apenas na primeira etapa — a confirmação pode ser executada diretamente por qualquer usuário autenticado. O objetivo é promover `wiener` a administrador. Credenciais de admin para reconhecimento: `administrator:admin`. Credenciais do alvo: `wiener:peter`.

### Conceito

Fluxos multi-etapa frequentemente têm controle de acesso implementado apenas no ponto de entrada, assumindo que quem chegou à etapa seguinte já foi devidamente autorizado. Essa suposição é falsa: cada etapa de um fluxo sensível deve verificar a autorização de forma independente. Um atacante pode pular a primeira etapa — onde o controle existe — e enviar diretamente a requisição da segunda etapa, onde ele não existe.

### Reconhecimento

Logado como `administrator`, o processo de promoção de um usuário foi executado e monitorado via Burp Proxy. Foram identificadas duas requisições distintas:

**Etapa 1 — Seleção:**
```
POST /admin-roles HTTP/1.1
username=carlos&action=upgrade
```

**Etapa 2 — Confirmação:**
```
POST /admin-roles HTTP/1.1
action=upgrade&confirmed=true&username=carlos
```

Ambas foram enviadas para o Burp Repeater para análise.

### Exploração

Logado como `wiener`, o cookie de sessão foi capturado. No Burp Repeater, as requisições foram modificadas:

- Cookie de sessão do `administrator` substituído pelo de `wiener`
- `username=carlos` alterado para `username=wiener` em ambas as requisições

Ao enviar a requisição da **Etapa 1**, o servidor retornou acesso negado — o controle de acesso estava presente.

Ao enviar diretamente a requisição da **Etapa 2** (pulando a etapa 1), o servidor processou normalmente e promoveu `wiener` a administrador, pois essa etapa não verificava autorização.

### Solução

Enviar diretamente a requisição de confirmação (etapa 2) com o cookie de sessão de `wiener` e `username=wiener`.

### O que aprendi

- Cada etapa de um fluxo sensível deve verificar autorização de forma independente — nunca assumir que etapas anteriores já garantiram o acesso
- Fluxos multi-etapa são alvos clássicos de bypass: mapear todas as requisições e testar cada uma individualmente com credenciais não autorizadas
- A mudança de resposta entre etapa 1 (bloqueada) e etapa 2 (aceita) revelou exatamente onde a proteção estava ausente

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

The admin panel uses a two-step flow to promote users: selection and confirmation. Access control was applied only to the first step — the confirmation can be executed directly by any authenticated user. The goal is to promote `wiener` to admin. Admin credentials for reconnaissance: `administrator:admin`. Target credentials: `wiener:peter`.

### Concept

Multi-step flows frequently have access control implemented only at the entry point, assuming that whoever reached the next step has already been properly authorized. This assumption is false: each step of a sensitive flow must independently verify authorization. An attacker can skip the first step — where the control exists — and directly send the second step's request, where it doesn't.

### Reconnaissance

Logged in as `administrator`, the user promotion process was executed and monitored via Burp Proxy. Two distinct requests were identified:

**Step 1 — Selection:**
```
POST /admin-roles HTTP/1.1
username=carlos&action=upgrade
```

**Step 2 — Confirmation:**
```
POST /admin-roles HTTP/1.1
action=upgrade&confirmed=true&username=carlos
```

Both were forwarded to Burp Repeater for analysis.

### Exploitation

Logged in as `wiener`, the session cookie was captured. In Burp Repeater, the requests were modified:

- `administrator`'s session cookie replaced with `wiener`'s
- `username=carlos` changed to `username=wiener` in both requests

Sending **Step 1**'s request returned access denied — access control was present.

Sending **Step 2**'s request directly (skipping step 1), the server processed it normally and promoted `wiener` to admin, as this step performed no authorization check.

### Solution

Send the confirmation request (step 2) directly with `wiener`'s session cookie and `username=wiener`.

### What I learned

- Each step of a sensitive flow must independently verify authorization — never assume previous steps already guaranteed access
- Multi-step flows are classic bypass targets: map all requests and test each individually with unauthorized credentials
- The response change between step 1 (blocked) and step 2 (accepted) revealed exactly where protection was absent

### References

- [PortSwigger — Access Control](https://portswigger.net/web-security/access-control)
- [OWASP — Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)
