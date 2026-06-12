# Lab 16 — SQL Injection with Filter Bypass via XML Encoding

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

**Plataforma:** PortSwigger Web Security Academy  
**Categoria:** SQL Injection — WAF Bypass  
**Dificuldade:** 🟡 Practitioner  
**Data:** 2026-06-07

### Descrição

A funcionalidade de verificação de estoque envia dados em XML e é vulnerável a SQL injection. Um WAF (Web Application Firewall) bloqueia payloads óbvios. O objetivo é contornar o WAF via obfuscação com entidades XML e extrair credenciais da tabela `users`.

### Conceito

WAFs geralmente analisam o texto literal da requisição em busca de padrões conhecidos (`UNION`, `SELECT`, etc.). Quando o payload está dentro de um contexto XML, é possível **codificar caracteres como entidades HTML/XML** (`&#85;` para `U`, `&#78;` para `N`, etc.) — o WAF não reconhece o padrão, mas o banco decodifica e executa normalmente.

A extensão **Hackvertor** do Burp Suite automatiza essa codificação.

### Reconhecimento

**Confirmando que o input é avaliado:**

```xml
<storeId>1+1</storeId>
```

A aplicação retornou o estoque da loja 2 — a expressão foi avaliada.

**Testando UNION (bloqueado pelo WAF):**

```xml
<storeId>1 UNION SELECT NULL</storeId>
```

Requisição bloqueada — WAF detectou o padrão.

### Exploração

**Passo 1 — Contornar o WAF com codificação de entidades:**

Usando o Hackvertor (Extensions → Hackvertor → Encode → hex_entities), o payload é codificado:

```xml
<storeId><@hex_entities>1 UNION SELECT NULL</@hex_entities></storeId>
```

Resposta normal — WAF não reconheceu o payload codificado, mas o banco o decodificou e executou.

**Passo 2 — Determinar número de colunas:**

Tentando retornar mais de uma coluna, a aplicação retornou `0 units` — a query retorna **uma única coluna**.

**Passo 3 — Extrair credenciais (concatenando em uma coluna):**

```xml
<storeId><@hex_entities>1 UNION SELECT username || '~' || password FROM users</@hex_entities></storeId>
```

As credenciais foram retornadas no formato `usuario~senha`. Login realizado com as credenciais do `administrator`.

### O que aprendi

- WAFs baseados em assinaturas podem ser bypassados com obfuscação — codificação de entidades XML é uma das técnicas mais eficazes nesse contexto
- O Hackvertor automatiza a codificação diretamente no Burp — economiza tempo e evita erros manuais
- Quando só há uma coluna disponível, concatenação com separador é o caminho para exfiltrar múltiplos campos
- WAF bypass não muda a vulnerabilidade subjacente — apenas o caminho para explorá-la

### Referências

- [PortSwigger — SQL Injection Filter Bypass](https://portswigger.net/web-security/sql-injection/lab-sql-injection-with-filter-bypass-via-xml-encoding)
- [Hackvertor Extension](https://portswigger.net/bappstore/65033cbd2c344fbabe57ac060b5dd100)

---

## 🇺🇸 English

**Platform:** PortSwigger Web Security Academy  
**Category:** SQL Injection — WAF Bypass  
**Difficulty:** 🟡 Practitioner  
**Date:** 2026-06-07

### Description

The stock check feature sends data in XML and is vulnerable to SQL injection. A WAF (Web Application Firewall) blocks obvious payloads. The goal is to bypass the WAF via XML entity obfuscation and extract credentials from the `users` table.

### Concept

WAFs typically analyze the literal request text looking for known patterns (`UNION`, `SELECT`, etc.). When the payload is inside an XML context, it's possible to **encode characters as HTML/XML entities** (`&#85;` for `U`, `&#78;` for `N`, etc.) — the WAF doesn't recognize the pattern, but the database decodes and executes it normally.

The **Hackvertor** Burp Suite extension automates this encoding.

### Reconnaissance

**Confirming input is evaluated:**

```xml
<storeId>1+1</storeId>
```

The application returned stock for store 2 — the expression was evaluated.

**Testing UNION (blocked by WAF):**

```xml
<storeId>1 UNION SELECT NULL</storeId>
```

Request blocked — WAF detected the pattern.

### Exploitation

**Step 1 — Bypass the WAF with entity encoding:**

Using Hackvertor (Extensions → Hackvertor → Encode → hex_entities), the payload is encoded:

```xml
<storeId><@hex_entities>1 UNION SELECT NULL</@hex_entities></storeId>
```

Normal response — WAF didn't recognize the encoded payload, but the database decoded and executed it.

**Step 2 — Determine column count:**

Attempting to return more than one column, the application returned `0 units` — the query returns **a single column**.

**Step 3 — Extract credentials (concatenating into one column):**

```xml
<storeId><@hex_entities>1 UNION SELECT username || '~' || password FROM users</@hex_entities></storeId>
```

Credentials were returned in `user~password` format. Login completed with the `administrator` credentials.

### What I learned

- Signature-based WAFs can be bypassed with obfuscation — XML entity encoding is one of the most effective techniques in this context
- Hackvertor automates encoding directly in Burp — saves time and avoids manual errors
- When only one column is available, concatenation with a separator is the way to exfiltrate multiple fields
- WAF bypass doesn't change the underlying vulnerability — only the path to exploit it

### References

- [PortSwigger — SQL Injection Filter Bypass](https://portswigger.net/web-security/sql-injection/lab-sql-injection-with-filter-bypass-via-xml-encoding)
- [Hackvertor Extension](https://portswigger.net/bappstore/65033cbd2c344fbabe57ac060b5dd100)
