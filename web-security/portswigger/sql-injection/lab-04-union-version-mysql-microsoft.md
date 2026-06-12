# Lab 04 — UNION Attack: Database Version on MySQL and Microsoft

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

**Plataforma:** PortSwigger Web Security Academy  
**Categoria:** SQL Injection — UNION Attack  
**Dificuldade:** 🟡 Practitioner  
**Data:** 2026-06-06

### Descrição

Mesmo cenário do lab anterior — filtro de categorias vulnerável a UNION attack — mas desta vez o banco é MySQL ou Microsoft SQL Server. O objetivo é exibir a versão do banco.

### Conceito

A sintaxe de comentários e as variáveis de versão variam entre bancos:

| Banco | Comentário | Versão |
|-------|-----------|--------|
| MySQL | `#` ou `-- ` | `@@version` |
| MSSQL | `--` | `@@version` |
| Oracle | `--` | `v$version` |
| PostgreSQL | `--` | `version()` |

No MySQL/MSSQL, `@@version` é uma variável global que retorna a versão do servidor.

### Reconhecimento

Confirmação de duas colunas aceitando texto, usando `#` como comentário (padrão MySQL):

```
'+UNION+SELECT+'abc','def'#
```

A ausência de erro confirmou a estrutura da query.

### Exploração

Com a estrutura confirmada, o payload consulta `@@version`:

```
'+UNION+SELECT+@@version,+NULL#
```

A versão do banco foi exibida na primeira coluna da resposta.

### O que aprendi

- `#` é o caractere de comentário padrão no MySQL — diferente do `--` usado em outros bancos
- `@@version` funciona tanto no MySQL quanto no MSSQL para obter a versão
- Identificar o tipo de banco é um passo crítico antes de construir qualquer UNION attack

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

Same scenario as the previous lab — vulnerable category filter with UNION attack — but this time the database is MySQL or Microsoft SQL Server. The goal is to display the database version.

### Concept

Comment syntax and version variables vary between databases:

| Database | Comment | Version |
|----------|---------|---------|
| MySQL | `#` or `-- ` | `@@version` |
| MSSQL | `--` | `@@version` |
| Oracle | `--` | `v$version` |
| PostgreSQL | `--` | `version()` |

In MySQL/MSSQL, `@@version` is a global variable returning the server version.

### Reconnaissance

Confirming two text-accepting columns using `#` as comment (MySQL standard):

```
'+UNION+SELECT+'abc','def'#
```

No error confirmed the query structure.

### Exploitation

With the structure confirmed, the payload queries `@@version`:

```
'+UNION+SELECT+@@version,+NULL#
```

The database version was displayed in the first column of the response.

### What I learned

- `#` is the standard comment character in MySQL — different from `--` used in other databases
- `@@version` works in both MySQL and MSSQL to retrieve the version
- Identifying the database type is a critical step before building any UNION attack

### References

- [PortSwigger — UNION Attacks](https://portswigger.net/web-security/sql-injection/union-attacks)
- [PortSwigger — SQL Injection Cheat Sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)
