# Lab 07 — UNION Attack: Determining Number of Columns

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

**Plataforma:** PortSwigger Web Security Academy  
**Categoria:** SQL Injection — UNION Attack  
**Dificuldade:** 🟡 Practitioner  
**Data:** 2026-06-06

### Descrição

Antes de qualquer UNION attack, é necessário saber exatamente quantas colunas a query original retorna. Este lab foca exclusivamente nessa etapa — o objetivo é retornar uma linha adicional contendo valores nulos.

### Conceito

Para que um `UNION SELECT` funcione, ele precisa retornar o mesmo número de colunas que a query original. A técnica padrão é adicionar `NULL`s incrementalmente até o servidor parar de retornar erro — cada `NULL` é compatível com qualquer tipo de dado, o que elimina problemas de tipo e isola a variável de quantidade.

### Reconhecimento

O filtro de categorias era o ponto de injeção. Iniciando com uma coluna:

```
'+UNION+SELECT+NULL--
```

Erro retornado — a query original tem mais de uma coluna.

### Exploração

Adicionando NULLs incrementalmente:

```
'+UNION+SELECT+NULL,NULL--
```

Ainda com erro.

```
'+UNION+SELECT+NULL,NULL,NULL--
```

Sem erro — a resposta incluiu uma linha extra com valores nulos. A query original retorna **três colunas**.

### O que aprendi

- `NULL` é o valor ideal para testar colunas em UNION attacks — compatível com qualquer tipo de dado
- O número de colunas deve ser determinado antes de qualquer outra etapa do ataque
- A resposta com erro vs sem erro é o único indicador necessário nesse processo

### Referências

- [PortSwigger — UNION Attacks](https://portswigger.net/web-security/sql-injection/union-attacks)

---

## 🇺🇸 English

**Platform:** PortSwigger Web Security Academy  
**Category:** SQL Injection — UNION Attack  
**Difficulty:** 🟡 Practitioner  
**Date:** 2026-06-06

### Description

Before any UNION attack, you need to know exactly how many columns the original query returns. This lab focuses exclusively on that step — the goal is to return an additional row containing null values.

### Concept

For a `UNION SELECT` to work, it must return the same number of columns as the original query. The standard technique is to add `NULL`s incrementally until the server stops returning an error — each `NULL` is compatible with any data type, which eliminates type issues and isolates the quantity variable.

### Reconnaissance

The category filter was the injection point. Starting with one column:

```
'+UNION+SELECT+NULL--
```

Error returned — the original query has more than one column.

### Exploitation

Adding NULLs incrementally:

```
'+UNION+SELECT+NULL,NULL--
```

Still error.

```
'+UNION+SELECT+NULL,NULL,NULL--
```

No error — the response included an extra row with null values. The original query returns **three columns**.

### What I learned

- `NULL` is the ideal value for testing columns in UNION attacks — compatible with any data type
- The column count must be determined before any other attack step
- Error vs no error response is the only indicator needed in this process

### References

- [PortSwigger — UNION Attacks](https://portswigger.net/web-security/sql-injection/union-attacks)
