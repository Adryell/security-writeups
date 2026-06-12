# Lab 10 — User ID Controlled by Request Parameter with Password Disclosure

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

**Plataforma:** PortSwigger Web Security Academy  
**Categoria:** Access Control / IDOR  
**Dificuldade:** 🟢 Apprentice  
**Data:** 2026-06-08

### Descrição

A página de conta exibe a senha atual do usuário em um campo mascarado. Combinando IDOR no parâmetro de URL com inspeção do HTML, é possível extrair a senha do administrador e usá-la para excluir `carlos`. Credenciais disponíveis: `wiener:peter`.

### Conceito

Campos de senha do tipo `type="password"` mascaram visualmente o valor no browser, mas o valor em si está no HTML — completamente legível via DevTools ou qualquer proxy. Enviar a senha atual pré-preenchida no HTML é uma falha grave por si só; combinada com IDOR no parâmetro de acesso à conta, o impacto se torna crítico: qualquer usuário pode acessar o perfil do administrador e extrair sua senha diretamente do código-fonte.

### Reconhecimento

Após o login com `wiener:peter`, a URL da conta era:

```
GET /my-account?id=wiener HTTP/1.1
```

O parâmetro `id` era manipulável — padrão identificado nos labs anteriores.

### Exploração

**Passo 1 — Acessar o perfil do administrador via IDOR:**

```
GET /my-account?id=administrator HTTP/1.1
```

A página do administrador foi retornada. Sem acesso ao painel admin ainda — apenas ao perfil.

**Passo 2 — Extrair a senha do HTML:**

Inspecionando o campo de senha no código-fonte da página:

```html
<input required type="password" name="password" value="kmbco815y4nzenpae66t">
```

A senha estava em texto claro no atributo `value`, apenas mascarada visualmente pelo `type="password"`.

**Passo 3 — Login e exclusão:**

Com as credenciais `administrator:kmbco815y4nzenpae66t`, o login foi realizado normalmente. Com acesso ao painel administrativo, o usuário `carlos` foi excluído.

### Solução

Acessar `/my-account?id=administrator`, extrair a senha do atributo `value` do campo de senha no HTML e fazer login como administrador para excluir `carlos`.

### O que aprendi

- `type="password"` é uma proteção visual, não de segurança — o valor está no DOM e é totalmente acessível
- Senhas nunca devem ser enviadas pré-preenchidas no HTML, nem mesmo em formato hash
- IDOR em parâmetros de conta + senha exposta no DOM = comprometimento total da conta
- A combinação de duas vulnerabilidades relativamente simples pode resultar em impacto crítico

### Referências

- [PortSwigger — IDOR](https://portswigger.net/web-security/access-control/idor)
- [OWASP — Sensitive Data Exposure](https://owasp.org/Top10/A02_2021-Cryptographic_Failures/)

---

## 🇺🇸 English

**Platform:** PortSwigger Web Security Academy  
**Category:** Access Control / IDOR  
**Difficulty:** 🟢 Apprentice  
**Date:** 2026-06-08

### Description

The account page displays the current user's password in a masked input field. By combining IDOR in the URL parameter with HTML inspection, it's possible to extract the admin's password and use it to delete `carlos`. Available credentials: `wiener:peter`.

### Concept

Password fields with `type="password"` visually mask the value in the browser, but the value itself is in the HTML — fully readable via DevTools or any proxy. Sending the current password pre-filled in the HTML is a severe flaw on its own; combined with IDOR in the account access parameter, the impact becomes critical: any user can access the admin's profile and extract their password directly from the source code.

### Reconnaissance

After logging in with `wiener:peter`, the account URL was:

```
GET /my-account?id=wiener HTTP/1.1
```

The `id` parameter was manipulable — a pattern identified in previous labs.

### Exploitation

**Step 1 — Access the admin profile via IDOR:**

```
GET /my-account?id=administrator HTTP/1.1
```

The admin's page was returned. No admin panel access yet — just the profile.

**Step 2 — Extract the password from the HTML:**

Inspecting the password field in the page source:

```html
<input required type="password" name="password" value="kmbco815y4nzenpae66t">
```

The password was in plaintext in the `value` attribute, only visually masked by `type="password"`.

**Step 3 — Login and deletion:**

Using credentials `administrator:kmbco815y4nzenpae66t`, login was completed normally. With admin panel access, user `carlos` was deleted.

### Solution

Access `/my-account?id=administrator`, extract the password from the `value` attribute of the password field in the HTML, then log in as admin to delete `carlos`.

### What I learned

- `type="password"` is a visual protection, not a security one — the value is in the DOM and fully accessible
- Passwords should never be sent pre-filled in HTML, not even in hashed form
- IDOR in account parameters + password exposed in the DOM = complete account compromise
- The combination of two relatively simple vulnerabilities can result in critical impact

### References

- [PortSwigger — IDOR](https://portswigger.net/web-security/access-control/idor)
- [OWASP — Sensitive Data Exposure](https://owasp.org/Top10/A02_2021-Cryptographic_Failures/)
