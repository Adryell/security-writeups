# Lab 04 — User Role Can Be Modified in User Profile

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

**Plataforma:** PortSwigger Web Security Academy  
**Categoria:** Access Control  
**Dificuldade:** 🟢 Apprentice  
**Data:** 2026-06-08

### Descrição

O painel administrativo em `/admin` é acessível apenas a usuários com `roleid` igual a 2. O objetivo é elevar o próprio papel para administrador e excluir o usuário `carlos`. Credenciais disponíveis: `wiener:peter`.

### Conceito

Quando a API retorna mais campos do que o necessário — como `roleid`, `apikey` e outros atributos internos — e o servidor aceita esses mesmos campos de volta em requisições do cliente, abre-se uma vulnerabilidade de **mass assignment** (ou **parameter pollution**). O cliente consegue modificar campos que nunca deveriam ser editáveis por ele, simplesmente incluindo-os no corpo da requisição.

### Reconhecimento

Após o login com `wiener:peter`, a funcionalidade de alteração de e-mail em *My Account* foi identificada como ponto de interesse. A requisição capturada via Burp Proxy enviava apenas:

```json
{
  "email": "teste@teste.com"
}
```

Ao encaminhar para o Burp Repeater e analisar a resposta do servidor, foi observado que a API retornava muito mais do que o esperado:

```json
{
  "username": "wiener",
  "email": "teste@teste.com",
  "apikey": "PfqDe0ufVeIQkhJFf3jzCJx58kjQH1aY",
  "roleid": 1
}
```

O campo `roleid` estava exposto na resposta — indicando que o servidor o processa e, potencialmente, também o aceita como input.

### Exploração

No Burp Repeater, a requisição foi modificada para incluir o campo `roleid` com o valor `2`:

```json
{
  "email": "teste@teste.com",
  "roleid": 2
}
```

O servidor aceitou a requisição e atualizou o papel do usuário sem qualquer validação adicional. Com `roleid: 2`, o acesso ao painel `/admin` foi concedido.

### Solução

Capturar a requisição de alteração de e-mail, adicionar `"roleid": 2` ao corpo JSON via Burp Repeater e acessar `/admin` para excluir o usuário `carlos`.

### O que aprendi

- APIs não devem retornar campos sensíveis desnecessários — o que não é exposto não pode ser explorado
- O servidor deve validar e filtrar os campos aceitos em cada endpoint — nunca confiar no corpo da requisição do cliente para atualizar campos privilegiados
- Mass assignment é uma vulnerabilidade comum em frameworks que mapeiam automaticamente o corpo da requisição para objetos do banco de dados

### Referências

- [PortSwigger — Access Control](https://portswigger.net/web-security/access-control)
- [OWASP — Mass Assignment](https://owasp.org/API-Security/editions/2023/en/0xa3-broken-object-property-level-authorization/)

---

## 🇺🇸 English

**Platform:** PortSwigger Web Security Academy  
**Category:** Access Control  
**Difficulty:** 🟢 Apprentice  
**Date:** 2026-06-08

### Description

The admin panel at `/admin` is accessible only to users with `roleid` equal to 2. The goal is to elevate your own role to admin and delete user `carlos`. Available credentials: `wiener:peter`.

### Concept

When an API returns more fields than necessary — such as `roleid`, `apikey`, and other internal attributes — and the server accepts those same fields back in client requests, a **mass assignment** (or **parameter pollution**) vulnerability is introduced. The client can modify fields that should never be user-editable simply by including them in the request body.

### Reconnaissance

After logging in with `wiener:peter`, the email change feature in *My Account* was identified as a point of interest. The request captured via Burp Proxy sent only:

```json
{
  "email": "teste@teste.com"
}
```

Forwarding to Burp Repeater and analyzing the server response revealed far more than expected:

```json
{
  "username": "wiener",
  "email": "teste@teste.com",
  "apikey": "PfqDe0ufVeIQkhJFf3jzCJx58kjQH1aY",
  "roleid": 1
}
```

The `roleid` field was exposed in the response — indicating the server processes it and potentially accepts it as input too.

### Exploitation

In Burp Repeater, the request was modified to include the `roleid` field with value `2`:

```json
{
  "email": "teste@teste.com",
  "roleid": 2
}
```

The server accepted the request and updated the user's role without any additional validation. With `roleid: 2`, access to `/admin` was granted.

### Solution

Capture the email update request, add `"roleid": 2` to the JSON body via Burp Repeater, then navigate to `/admin` and delete user `carlos`.

### What I learned

- APIs should not return unnecessary sensitive fields — what isn't exposed can't be exploited
- The server must validate and filter accepted fields per endpoint — never trust the client request body to update privileged fields
- Mass assignment is a common vulnerability in frameworks that automatically map request bodies to database objects

### References

- [PortSwigger — Access Control](https://portswigger.net/web-security/access-control)
- [OWASP — Mass Assignment](https://owasp.org/API-Security/editions/2023/en/0xa3-broken-object-property-level-authorization/)
