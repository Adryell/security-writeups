# Lab 08 — UNION Attack: Finding a Column Containing Text

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

**Plataforma:** PortSwigger Web Security Academy  
**Categoria:** SQL Injection — UNION Attack  
**Dificuldade:** 🟡 Practitioner  
**Data:** 2026-06-07

### Descrição

Após determinar o número de colunas, o próximo passo é descobrir quais delas aceitam dados do tipo texto — pré-requisito para exfiltrar informações em formato legível. O lab fornece um valor aleatório que precisa aparecer na resposta.

### Conceito

Nem todas as colunas de uma query aceitam strings. Algumas podem ser do tipo inteiro ou outro tipo incompatível com texto. Para identificar as colunas que aceitam strings, substitui-se cada `NULL` por um valor de texto e observa-se qual não gera erro.

### Reconhecimento

A query retorna três colunas (determinado no lab anterior):

```
'+UNION+SELECT+NULL,NULL,NULL--
```

### Exploração

Substituindo cada `NULL` pelo valor fornecido pelo lab, um de cada vez:

```
'+UNION+SELECT+'abcdef',NULL,NULL--
```

Erro — primeira coluna não aceita texto.

```
'+UNION+SELECT+NULL,'abcdef',NULL--
```

Sem erro — o valor apareceu na resposta. A **segunda coluna** é compatível com texto.

### O que aprendi

- Após determinar o número de colunas, sempre verificar quais aceitam texto antes de tentar exfiltrar dados
- Substituir um NULL por vez isola a variável e evita ambiguidade na interpretação da resposta
- Colunas numéricas rejeitam strings — o erro é o sinal de incompatibilidade

### Referências

- [PortSwigger — UNION Attacks](https://portswigger.net/web-security/sql-injection/union-attacks)

---

## 🇺🇸 English

**Platform:** PortSwigger Web Security Academy  
**Category:** SQL Injection — UNION Attack  
**Difficulty:** 🟡 Practitioner  
**Date:** 2026-06-07

### Description

After determining the number of columns, the next step is finding which ones accept text data — a prerequisite for exfiltrating readable information. The lab provides a random value that needs to appear in the response.

### Concept

Not all columns in a query accept strings. Some may be of integer or other types incompatible with text. To identify text-accepting columns, each `NULL` is replaced with a text value and the response is observed for errors.

### Reconnaissance

The query returns three columns (determined in the previous lab):

```
'+UNION+SELECT+NULL,NULL,NULL--
```

### Exploitation

Replacing each `NULL` with the lab-provided value, one at a time:

```
'+UNION+SELECT+'abcdef',NULL,NULL--
```

Error — first column doesn't accept text.

```
'+UNION+SELECT+NULL,'abcdef',NULL--
```

No error — the value appeared in the response. The **second column** is text-compatible.

### What I learned

- After determining column count, always verify which ones accept text before attempting data exfiltration
- Replacing one NULL at a time isolates the variable and avoids ambiguity in interpreting the response
- Numeric columns reject strings — the error is the incompatibility signal

### References

- [PortSwigger — UNION Attacks](https://portswigger.net/web-security/sql-injection/union-attacks)
