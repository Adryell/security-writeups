# Lab 11 — Insecure Direct Object References (IDOR)

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

**Plataforma:** PortSwigger Web Security Academy  
**Categoria:** Access Control / IDOR  
**Dificuldade:** 🟢 Apprentice  
**Data:** 2026-06-08

### Descrição

A aplicação armazena transcrições de chat diretamente no sistema de arquivos do servidor e as serve via URLs estáticas com nomes de arquivo sequenciais. O objetivo é acessar a transcrição de outro usuário e encontrar a senha de `carlos`.

### Conceito

Quando arquivos são servidos diretamente do sistema de arquivos com nomes previsíveis — como `1.txt`, `2.txt`, `3.txt` — qualquer usuário pode tentar acessar arquivos que não pertencem a ele simplesmente alterando o nome na URL. Não há verificação de propriedade: o servidor entrega qualquer arquivo cujo nome seja conhecido. A sequencialidade dos nomes torna a enumeração trivial.

### Reconhecimento

Ao usar o chat e baixar a transcrição, o arquivo baixado se chamava `2.txt`. Baixando novamente, o nome subiu para `3.txt`, depois `4.txt` — confirmando que os nomes são sequenciais e incrementais.

A ausência de um `1.txt` foi o indicador crítico: havia uma transcrição anterior, de outra sessão, que nunca foi baixada — e provavelmente pertencia a outro usuário.

### Exploração

A requisição de download foi capturada via Burp Proxy:

```
GET /download-transcript/2.txt HTTP/1.1
```

O nome do arquivo foi alterado para:

```
GET /download-transcript/1.txt HTTP/1.1
```

O servidor retornou o arquivo sem qualquer verificação de propriedade. O conteúdo era a transcrição de uma conversa de `carlos`, que continha sua senha em texto claro.

### Solução

Capturar a requisição de download, alterar o nome do arquivo para `1.txt` e extrair a senha de `carlos` da transcrição retornada.

### O que aprendi

- Nomes de arquivo sequenciais em URLs são um convite à enumeração — sempre testar valores anteriores e posteriores ao identificado
- Arquivos no sistema de arquivos do servidor devem ter controle de acesso — não basta restringir a interface, é necessário verificar a propriedade no endpoint de download
- A ausência de um registro esperado (o `1.txt` faltante) foi o indicador da vulnerabilidade

### Referências

- [PortSwigger — IDOR](https://portswigger.net/web-security/access-control/idor)
- [OWASP — Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)

---

## 🇺🇸 English

**Platform:** PortSwigger Web Security Academy  
**Category:** Access Control / IDOR  
**Difficulty:** 🟢 Apprentice  
**Date:** 2026-06-08

### Description

The application stores chat transcripts directly on the server's filesystem and serves them via static URLs with sequential filenames. The goal is to access another user's transcript and find `carlos`'s password.

### Concept

When files are served directly from the filesystem with predictable names — like `1.txt`, `2.txt`, `3.txt` — any user can attempt to access files that don't belong to them simply by changing the filename in the URL. There's no ownership verification: the server delivers any file whose name is known. The sequential naming makes enumeration trivial.

### Reconnaissance

Using the chat and downloading the transcript, the downloaded file was named `2.txt`. Downloading again, the name incremented to `3.txt`, then `4.txt` — confirming names are sequential and incremental.

The absence of a `1.txt` was the critical indicator: there was a previous transcript, from another session, that was never downloaded — and likely belonged to another user.

### Exploitation

The download request was captured via Burp Proxy:

```
GET /download-transcript/2.txt HTTP/1.1
```

The filename was changed to:

```
GET /download-transcript/1.txt HTTP/1.1
```

The server returned the file without any ownership verification. The content was a transcript of `carlos`'s conversation, which contained his password in plaintext.

### Solution

Capture the download request, change the filename to `1.txt`, and extract `carlos`'s password from the returned transcript.

### What I learned

- Sequential filenames in URLs invite enumeration — always test values before and after the identified one
- Files on the server's filesystem must have access control — restricting the UI is not enough, ownership must be verified at the download endpoint
- The absence of an expected record (the missing `1.txt`) was the vulnerability indicator

### References

- [PortSwigger — IDOR](https://portswigger.net/web-security/access-control/idor)
- [OWASP — Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)
