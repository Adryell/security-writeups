# Lab 09 — UNION Attack: Retrieving Data from Other Tables

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

**Plataforma:** PortSwigger Web Security Academy  
**Categoria:** SQL Injection — UNION Attack  
**Dificuldade:** 🟡 Practitioner  
**Data:** 2026-06-07

### Descrição

Aplicando as técnicas dos labs anteriores — número de colunas e colunas com texto — agora o objetivo é extrair dados da tabela `users` e fazer login como `administrator`.

### Conceito

Com o número de colunas e os tipos mapeados, um UNION attack pode apontar para qualquer tabela acessível pelo usuário do banco. Neste lab, a tabela alvo é conhecida (`users`) com colunas `username` e `password` — o que simplifica a exploração.

### Reconhecimento

Confirmação de duas colunas, ambas aceitando texto:

```
'+UNION+SELECT+'abc','def'--
```

Sem erro — estrutura mapeada.

### Exploração

Com a estrutura conhecida, o payload aponta diretamente para a tabela `users`:

```
'+UNION+SELECT+username,+password+FROM+users--
```

A resposta retornou todos os pares `username:password` da tabela. A senha do `administrator` estava entre eles.

### O que aprendi

- Quando a estrutura da tabela alvo é conhecida, o UNION attack é direto — colunas mapeadas, payload construído, dados exfiltrados
- Em cenários reais, a etapa de enumeração de schema (labs 05 e 06) precederia este passo
- Dois campos em duas colunas distintas é o caso mais simples — o lab seguinte aborda quando só há uma coluna disponível

### Referências

- [PortSwigger — UNION Attacks](https://portswigger.net/web-security/sql-injection/union-attacks)

---

## 🇺🇸 English

**Platform:** PortSwigger Web Security Academy  
**Category:** SQL Injection — UNION Attack  
**Difficulty:** 🟡 Practitioner  
**Date:** 2026-06-07

### Description

Applying techniques from previous labs — column count and text columns — the goal now is to extract data from the `users` table and log in as `administrator`.

### Concept

With column count and types mapped, a UNION attack can target any table accessible by the database user. In this lab, the target table is known (`users`) with columns `username` and `password` — which simplifies exploitation.

### Reconnaissance

Confirming two columns, both accepting text:

```
'+UNION+SELECT+'abc','def'--
```

No error — structure mapped.

### Exploitation

With the known structure, the payload targets the `users` table directly:

```
'+UNION+SELECT+username,+password+FROM+users--
```

The response returned all `username:password` pairs from the table. The `administrator` password was among them.

### What I learned

- When the target table structure is known, the UNION attack is direct — columns mapped, payload built, data exfiltrated
- In real scenarios, the schema enumeration step (labs 05 and 06) would precede this
- Two fields in two separate columns is the simplest case — the next lab covers when only one column is available

### References

- [PortSwigger — UNION Attacks](https://portswigger.net/web-security/sql-injection/union-attacks)
