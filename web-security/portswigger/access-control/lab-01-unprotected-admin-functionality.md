# Lab 01 — Unprotected Admin Functionality

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

**Plataforma:** PortSwigger Web Security Academy  
**Categoria:** Access Control  
**Dificuldade:** 🟢 Apprentice  
**Data:** 2026-06-08

### Descrição

A aplicação possui um painel administrativo completamente desprotegido — sem autenticação, sem verificação de papel. O objetivo é localizar esse painel e excluir o usuário `carlos`.

### Conceito

Painéis administrativos expostos sem qualquer camada de autenticação representam uma falha crítica de controle de acesso. Muitas aplicações tentam "esconder" essas rotas por obscuridade, usando caminhos não óbvios — o que não é uma medida de segurança eficaz. O arquivo `robots.txt`, criado para orientar crawlers de busca, frequentemente expõe inadvertidamente rotas sensíveis que o desenvolvedor não queria indexar, mas também não protegeu de verdade.

### Reconhecimento

O primeiro passo foi verificar o arquivo `robots.txt`, um alvo clássico em reconhecimento web:

```
GET /robots.txt HTTP/1.1
```

Resposta:
```
Disallow: /administrator-panel
```

O próprio arquivo indicou o caminho do painel administrativo.

### Exploração

Com o caminho em mãos, o acesso foi direto:

```
GET /administrator-panel HTTP/1.1
```

Nenhuma verificação de autenticação foi realizada pelo servidor. O painel foi carregado normalmente, com a opção de exclusão de usuários disponível sem qualquer restrição.

### Solução

Acessar `/administrator-panel` e excluir o usuário `carlos` através da interface disponível.

### O que aprendi

- `robots.txt` é frequentemente o primeiro arquivo a verificar em reconhecimento — ele pode revelar rotas que o desenvolvedor quis ocultar dos crawlers, mas esqueceu de proteger de verdade
- Segurança por obscuridade não é segurança: esconder uma rota não substitui autenticação
- Todo endpoint sensível deve exigir verificação de identidade e papel, independentemente de estar "escondido"

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

The application has a completely unprotected admin panel — no authentication, no role verification. The goal is to locate it and delete the user `carlos`.

### Concept

Exposed admin panels with no authentication layer represent a critical access control failure. Many applications attempt to "hide" these routes through obscurity using non-obvious paths — which is not an effective security measure. The `robots.txt` file, designed to guide search crawlers, frequently inadvertently exposes sensitive routes that developers didn't want indexed but also didn't properly protect.

### Reconnaissance

The first step was checking `robots.txt`, a classic web recon target:

```
GET /robots.txt HTTP/1.1
```

Response:
```
Disallow: /administrator-panel
```

The file itself revealed the admin panel path.

### Exploitation

With the path in hand, access was direct:

```
GET /administrator-panel HTTP/1.1
```

No authentication check was performed by the server. The panel loaded normally with user deletion options available without any restriction.

### Solution

Navigate to `/administrator-panel` and delete user `carlos` through the available interface.

### What I learned

- `robots.txt` is often the first file to check during recon — it can reveal routes the developer wanted to hide from crawlers but forgot to actually protect
- Security through obscurity is not security: hiding a route doesn't replace authentication
- Every sensitive endpoint must require identity and role verification, regardless of whether it's "hidden"

### References

- [PortSwigger — Access Control](https://portswigger.net/web-security/access-control)
- [OWASP — Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)
