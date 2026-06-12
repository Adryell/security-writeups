# Lab 02 — SQL Injection: Login Bypass

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

**Plataforma:** PortSwigger Web Security Academy  
**Categoria:** SQL Injection  
**Dificuldade:** 🟢 Apprentice  
**Data:** 2026-06-06

### Descrição

A função de login é vulnerável a SQL injection. O objetivo é autenticar como o usuário `administrator` sem conhecer a senha.

### Conceito

Formulários de login geralmente constroem uma query do tipo:

```sql
SELECT * FROM users WHERE username = 'input' AND password = 'input'
```

Se o campo `username` não for sanitizado, é possível injetar um comentário que descarta a verificação de senha inteiramente — tornando qualquer senha válida para o usuário alvo.

### Reconhecimento

A página de login enviava as credenciais via POST. O campo `username` era processado diretamente na query sem uso de prepared statements.

### Exploração

Ao interceptar a requisição no Burp Suite e alterar o `username` para:

```
administrator'--
```

A query resultante no banco se torna:

```sql
SELECT * FROM users WHERE username = 'administrator'--' AND password = 'qualquer_coisa'
```

O `--` comenta a verificação de senha. O banco retorna o registro do administrador sem checar a senha, e o login é concedido.

### O que aprendi

- Comentários SQL (`--`) podem ser usados para truncar queries e eliminar condições de segurança
- Formulários de login sem prepared statements são um dos vetores mais clássicos de SQLi
- Não é necessário saber a senha — basta conhecer o username e injetar o comentário

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

The login function is vulnerable to SQL injection. The goal is to authenticate as the `administrator` user without knowing the password.

### Concept

Login forms typically build a query like:

```sql
SELECT * FROM users WHERE username = 'input' AND password = 'input'
```

If the `username` field isn't sanitized, it's possible to inject a comment that discards the password check entirely — making any password valid for the target user.

### Reconnaissance

The login page sent credentials via POST. The `username` field was processed directly in the query without prepared statements.

### Exploitation

Intercepting the request in Burp Suite and changing the `username` to:

```
administrator'--
```

The resulting database query becomes:

```sql
SELECT * FROM users WHERE username = 'administrator'--' AND password = 'anything'
```

The `--` comments out the password check. The database returns the admin record without verifying the password, and login is granted.

### What I learned

- SQL comments (`--`) can be used to truncate queries and eliminate security conditions
- Login forms without prepared statements are one of the most classic SQLi vectors
- Knowing the password isn't necessary — just the username and the comment injection

### References

- [PortSwigger — SQL Injection](https://portswigger.net/web-security/sql-injection)
- [OWASP — SQL Injection](https://owasp.org/www-community/attacks/SQL_Injection)
