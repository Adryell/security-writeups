# Lab 03 — UNION Attack: Database Version on Oracle

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

**Plataforma:** PortSwigger Web Security Academy  
**Categoria:** SQL Injection — UNION Attack  
**Dificuldade:** 🟡 Practitioner  
**Data:** 2026-06-06

### Descrição

O filtro de categorias é vulnerável a SQL injection. O objetivo é usar um UNION attack para exibir a string de versão do banco de dados Oracle.

### Conceito

Um UNION attack permite anexar uma query adicional ao resultado da original, desde que:
1. O número de colunas seja idêntico
2. Os tipos de dados sejam compatíveis

Em bancos Oracle, toda instrução `SELECT` precisa de uma cláusula `FROM` — mesmo que não seja necessário consultar uma tabela real. Para isso, existe a tabela built-in `dual`, usada justamente nesses casos.

### Reconhecimento

Primeiro, foi necessário descobrir quantas colunas a query retorna. Testando com `NULL`:

```
'+UNION+SELECT+'abc','def'+FROM+dual--
```

A ausência de erro confirmou que a query retorna **duas colunas**, ambas aceitando texto.

### Exploração

Com o número de colunas confirmado, o payload final consulta a view `v$version`, que armazena informações da versão no Oracle:

```
'+UNION+SELECT+BANNER,+NULL+FROM+v$version--
```

A string de versão do banco foi exibida na resposta da aplicação.

### O que aprendi

- UNION attacks exigem correspondência exata de colunas e tipos — testar com `NULL` primeiro é o caminho padrão
- Oracle obriga a cláusula `FROM` em todo `SELECT` — a tabela `dual` resolve isso
- `v$version` é a view padrão para consultar a versão no Oracle

### Referências

- [PortSwigger — UNION Attacks](https://portswigger.net/web-security/sql-injection/union-attacks)
- [PortSwigger — SQL Injection Cheat Sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)

---

## 🇺🇸 English

**Platform:** PortSwigger Web Security Academy  
**Category:** SQL Injection — UNION Attack  
**Difficulty:** 🟡 Practitioner  
**Date:** 2026-06-06

### Description

The category filter is vulnerable to SQL injection. The goal is to use a UNION attack to display the Oracle database version string.

### Concept

A UNION attack allows appending an additional query to the original result, as long as:
1. The number of columns is identical
2. The data types are compatible

In Oracle databases, every `SELECT` statement requires a `FROM` clause — even when querying no real table. For this, the built-in `dual` table exists.

### Reconnaissance

First, it was necessary to find out how many columns the query returns. Testing with `NULL`:

```
'+UNION+SELECT+'abc','def'+FROM+dual--
```

No error confirmed the query returns **two columns**, both accepting text.

### Exploitation

With the column count confirmed, the final payload queries `v$version`, which stores Oracle version information:

```
'+UNION+SELECT+BANNER,+NULL+FROM+v$version--
```

The database version string was displayed in the application response.

### What I learned

- UNION attacks require exact column and type matching — testing with `NULL` first is the standard approach
- Oracle requires a `FROM` clause in every `SELECT` — the `dual` table solves this
- `v$version` is the standard view for querying version info in Oracle

### References

- [PortSwigger — UNION Attacks](https://portswigger.net/web-security/sql-injection/union-attacks)
- [PortSwigger — SQL Injection Cheat Sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)
