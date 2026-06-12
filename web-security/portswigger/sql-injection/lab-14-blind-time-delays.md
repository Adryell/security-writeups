# Lab 14 — Blind SQL Injection with Time Delays

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

**Plataforma:** PortSwigger Web Security Academy  
**Categoria:** SQL Injection — Blind / Time-Based  
**Dificuldade:** 🟡 Practitioner  
**Data:** 2026-06-07

### Descrição

A aplicação é vulnerável a blind SQLi, mas não há variação de conteúdo nem erros detectáveis. O único canal disponível é o **tempo de resposta**. O objetivo é provocar um atraso de 10 segundos na resposta.

### Conceito

**Time-based blind SQLi** é o último recurso quando não há nenhum canal de inferência visível. A técnica consiste em injetar uma função de sleep no banco — se a query for executada, a resposta demora o tempo programado. Isso confirma tanto a existência da injeção quanto o tipo de banco.

A função varia por banco:

| Banco | Função de delay |
|-------|----------------|
| PostgreSQL | `pg_sleep(n)` |
| MySQL | `SLEEP(n)` |
| MSSQL | `WAITFOR DELAY '0:0:n'` |
| Oracle | `dbms_pipe.receive_message(('a'),n)` |

### Exploração

O payload usa concatenação (`||`) para injetar `pg_sleep(10)` — confirmando que o banco é PostgreSQL:

```
TrackingId=x'||pg_sleep(10)--
```

A aplicação levou exatamente 10 segundos para responder — confirmando a injeção e o tipo de banco.

### O que aprendi

- Time-based SQLi é detectável mesmo quando não há nenhuma diferença visual na resposta
- `pg_sleep()` é específico do PostgreSQL — a função correta depende do banco-alvo
- O atraso confirma a injeção: se o banco não executasse a query, o sleep não ocorreria
- Este lab é introdutório — o próximo aplica a mesma técnica para exfiltração real de dados

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

The application is vulnerable to blind SQLi, but there's no content variation or detectable errors. The only available channel is **response time**. The goal is to cause a 10-second delay in the response.

### Concept

**Time-based blind SQLi** is the last resort when there's no visible inference channel. The technique injects a sleep function into the database — if the query executes, the response takes the programmed time. This confirms both the injection's existence and the database type.

The function varies by database:

| Database | Delay function |
|----------|---------------|
| PostgreSQL | `pg_sleep(n)` |
| MySQL | `SLEEP(n)` |
| MSSQL | `WAITFOR DELAY '0:0:n'` |
| Oracle | `dbms_pipe.receive_message(('a'),n)` |

### Exploitation

The payload uses concatenation (`||`) to inject `pg_sleep(10)` — confirming the database is PostgreSQL:

```
TrackingId=x'||pg_sleep(10)--
```

The application took exactly 10 seconds to respond — confirming the injection and database type.

### What I learned

- Time-based SQLi is detectable even when there's no visual difference in the response
- `pg_sleep()` is PostgreSQL-specific — the correct function depends on the target database
- The delay confirms the injection: if the database didn't execute the query, the sleep wouldn't occur
- This lab is introductory — the next one applies the same technique for real data exfiltration

### References

- [PortSwigger — Blind SQL Injection](https://portswigger.net/web-security/sql-injection/blind)
- [PortSwigger — SQL Injection Cheat Sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)
