# 🔐 Security Writeups

![Labs](https://img.shields.io/badge/Labs%20Resolvidos-29-blue?style=flat-square&logo=hackthebox&logoColor=white)
![Platform](https://img.shields.io/badge/PortSwigger-Academy-orange?style=flat-square&logo=google-chrome&logoColor=white)
![Focus](https://img.shields.io/badge/Focus-Web%20Security-blueviolet?style=flat-square)
![Status](https://img.shields.io/badge/Status-Em%20progresso-green?style=flat-square)

> 🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

---

## 🇧🇷 Português

Documentação dos meus estudos e labs práticos em segurança ofensiva, com foco em pentest e aplicações web.

### Estrutura

```
security-writeups/
└── web-security/
    └── portswigger/
        ├── access-control/
        └── sql-injection/
```

### 📊 Progresso por Categoria

| Categoria | Progresso | Apprentice | Practitioner | Total |
|-----------|-----------|-----------|--------------|-------|
| Access Control & IDOR | `██████████` 100% | 9 ✅ | 4 ✅ | **13** |
| SQL Injection | `██████████` 100% | 2 ✅ | 14 ✅ | **16** |
| XSS | `░░░░░░░░░░` 0% | — | — | **em breve** |

### Writeups — Access Control & IDOR

| # | Lab | Dificuldade | Status |
|---|-----|-------------|--------|
| 01 | [Unprotected Admin Functionality](./web-security/access-control/lab-01-unprotected-admin-functionality.md) | 🟢 Apprentice | ✅ |
| 02 | [Unprotected Admin with Unpredictable URL](./web-security/access-control/lab-02-unprotected-admin-unpredictable-url.md) | 🟢 Apprentice | ✅ |
| 03 | [User Role Controlled by Request Parameter](./web-security/access-control/lab-03-user-role-request-parameter.md) | 🟢 Apprentice | ✅ |
| 04 | [User Role Modified in User Profile](./web-security/access-control/lab-04-user-role-modified-user-profile.md) | 🟢 Apprentice | ✅ |
| 05 | [URL-Based Access Control Bypass](./web-security/access-control/lab-05-url-based-access-control-bypass.md) | 🟡 Practitioner | ✅ |
| 06 | [Method-Based Access Control Bypass](./web-security/access-control/lab-06-method-based-access-control-bypass.md) | 🟡 Practitioner | ✅ |
| 07 | [User ID Controlled by Request Parameter](./web-security/access-control/lab-07-userid-request-parameter.md) | 🟢 Apprentice | ✅ |
| 08 | [User ID with Unpredictable GUIDs](./web-security/access-control/lab-08-userid-unpredictable-guids.md) | 🟢 Apprentice | ✅ |
| 09 | [User ID with Data Leakage in Redirect](./web-security/access-control/lab-09-userid-data-leakage-redirect.md) | 🟢 Apprentice | ✅ |
| 10 | [User ID with Password Disclosure](./web-security/access-control/lab-10-userid-password-disclosure.md) | 🟢 Apprentice | ✅ |
| 11 | [Insecure Direct Object References (IDOR)](./web-security/access-control/lab-11-insecure-direct-object-references.md) | 🟢 Apprentice | ✅ |
| 12 | [Multi-Step Process with No Access Control](./web-security/access-control/lab-12-multi-step-no-access-control.md) | 🟡 Practitioner | ✅ |
| 13 | [Referer-Based Access Control](./web-security/access-control/lab-13-referer-based-access-control.md) | 🟡 Practitioner | ✅ |

### Writeups — SQL Injection

| # | Lab | Dificuldade | Status |
|---|-----|-------------|--------|
| 01 | [WHERE Clause — Retrieving Hidden Data](./web-security/sql-injection/lab-01-where-clause-hidden-data.md) | 🟢 Apprentice | ✅ |
| 02 | [Login Bypass](./web-security/sql-injection/lab-02-login-bypass.md) | 🟢 Apprentice | ✅ |
| 03 | [UNION — Database Version (Oracle)](./web-security/sql-injection/lab-03-union-version-oracle.md) | 🟡 Practitioner | ✅ |
| 04 | [UNION — Database Version (MySQL/Microsoft)](./web-security/sql-injection/lab-04-union-version-mysql-microsoft.md) | 🟡 Practitioner | ✅ |
| 05 | [UNION — Listing Contents (Non-Oracle)](./web-security/sql-injection/lab-05-listing-contents-non-oracle.md) | 🟡 Practitioner | ✅ |
| 06 | [UNION — Listing Contents (Oracle)](./web-security/sql-injection/lab-06-listing-contents-oracle.md) | 🟡 Practitioner | ✅ |
| 07 | [UNION — Determining Number of Columns](./web-security/sql-injection/lab-07-union-number-of-columns.md) | 🟡 Practitioner | ✅ |
| 08 | [UNION — Finding a Column Containing Text](./web-security/sql-injection/lab-08-union-find-text-column.md) | 🟡 Practitioner | ✅ |
| 09 | [UNION — Retrieving Data from Other Tables](./web-security/sql-injection/lab-09-union-retrieve-data-other-tables.md) | 🟡 Practitioner | ✅ |
| 10 | [UNION — Multiple Values in a Single Column](./web-security/sql-injection/lab-10-union-multiple-values-single-column.md) | 🟡 Practitioner | ✅ |
| 11 | [Blind — Conditional Responses](./web-security/sql-injection/lab-11-blind-conditional-responses.md) | 🟡 Practitioner | ✅ |
| 12 | [Blind — Conditional Errors](./web-security/sql-injection/lab-12-blind-conditional-errors.md) | 🟡 Practitioner | ✅ |
| 13 | [Visible Error-Based](./web-security/sql-injection/lab-13-visible-error-based.md) | 🟡 Practitioner | ✅ |
| 14 | [Blind — Time Delays](./web-security/sql-injection/lab-14-blind-time-delays.md) | 🟡 Practitioner | ✅ |
| 15 | [Blind — Time Delays and Information Retrieval](./web-security/sql-injection/lab-15-blind-time-delays-info-retrieval.md) | 🟡 Practitioner | ✅ |
| 16 | [Filter Bypass via XML Encoding](./web-security/sql-injection/lab-16-filter-bypass-xml-encoding.md) | 🟡 Practitioner | ✅ |

### Plataformas de estudo

- [PortSwigger Web Security Academy](https://portswigger.net/web-security)

---

## 🇺🇸 English

Documentation of my practical studies and labs in offensive security, focused on pentesting and web applications.

### Structure

```
security-writeups/
└── web-security/
    └── portswigger/
        ├── access-control/
        └── sql-injection/
```

### 📊 Progress by Category

| Category | Progress | Apprentice | Practitioner | Total |
|----------|----------|-----------|--------------|-------|
| Access Control & IDOR | `██████████` 100% | 9 ✅ | 4 ✅ | **13** |
| SQL Injection | `██████████` 100% | 2 ✅ | 14 ✅ | **16** |
| XSS | `░░░░░░░░░░` 0% | — | — | **coming soon** |

### Writeups — Access Control & IDOR

| # | Lab | Difficulty | Status |
|---|-----|------------|--------|
| 01 | [Unprotected Admin Functionality](./web-security/access-control/lab-01-unprotected-admin-functionality.md) | 🟢 Apprentice | ✅ |
| 02 | [Unprotected Admin with Unpredictable URL](./web-security/access-control/lab-02-unprotected-admin-unpredictable-url.md) | 🟢 Apprentice | ✅ |
| 03 | [User Role Controlled by Request Parameter](./web-security/access-control/lab-03-user-role-request-parameter.md) | 🟢 Apprentice | ✅ |
| 04 | [User Role Modified in User Profile](./web-security/access-control/lab-04-user-role-modified-user-profile.md) | 🟢 Apprentice | ✅ |
| 05 | [URL-Based Access Control Bypass](./web-security/access-control/lab-05-url-based-access-control-bypass.md) | 🟡 Practitioner | ✅ |
| 06 | [Method-Based Access Control Bypass](./web-security/access-control/lab-06-method-based-access-control-bypass.md) | 🟡 Practitioner | ✅ |
| 07 | [User ID Controlled by Request Parameter](./web-security/access-control/lab-07-userid-request-parameter.md) | 🟢 Apprentice | ✅ |
| 08 | [User ID with Unpredictable GUIDs](./web-security/access-control/lab-08-userid-unpredictable-guids.md) | 🟢 Apprentice | ✅ |
| 09 | [User ID with Data Leakage in Redirect](./web-security/access-control/lab-09-userid-data-leakage-redirect.md) | 🟢 Apprentice | ✅ |
| 10 | [User ID with Password Disclosure](./web-security/access-control/lab-10-userid-password-disclosure.md) | 🟢 Apprentice | ✅ |
| 11 | [Insecure Direct Object References (IDOR)](./web-security/access-control/lab-11-insecure-direct-object-references.md) | 🟢 Apprentice | ✅ |
| 12 | [Multi-Step Process with No Access Control](./web-security/access-control/lab-12-multi-step-no-access-control.md) | 🟡 Practitioner | ✅ |
| 13 | [Referer-Based Access Control](./web-security/access-control/lab-13-referer-based-access-control.md) | 🟡 Practitioner | ✅ |

### Writeups — SQL Injection

| # | Lab | Difficulty | Status |
|---|-----|------------|--------|
| 01 | [WHERE Clause — Retrieving Hidden Data](./web-security/sql-injection/lab-01-where-clause-hidden-data.md) | 🟢 Apprentice | ✅ |
| 02 | [Login Bypass](./web-security/sql-injection/lab-02-login-bypass.md) | 🟢 Apprentice | ✅ |
| 03 | [UNION — Database Version (Oracle)](./web-security/sql-injection/lab-03-union-version-oracle.md) | 🟡 Practitioner | ✅ |
| 04 | [UNION — Database Version (MySQL/Microsoft)](./web-security/sql-injection/lab-04-union-version-mysql-microsoft.md) | 🟡 Practitioner | ✅ |
| 05 | [UNION — Listing Contents (Non-Oracle)](./web-security/sql-injection/lab-05-listing-contents-non-oracle.md) | 🟡 Practitioner | ✅ |
| 06 | [UNION — Listing Contents (Oracle)](./web-security/sql-injection/lab-06-listing-contents-oracle.md) | 🟡 Practitioner | ✅ |
| 07 | [UNION — Determining Number of Columns](./web-security/sql-injection/lab-07-union-number-of-columns.md) | 🟡 Practitioner | ✅ |
| 08 | [UNION — Finding a Column Containing Text](./web-security/sql-injection/lab-08-union-find-text-column.md) | 🟡 Practitioner | ✅ |
| 09 | [UNION — Retrieving Data from Other Tables](./web-security/sql-injection/lab-09-union-retrieve-data-other-tables.md) | 🟡 Practitioner | ✅ |
| 10 | [UNION — Multiple Values in a Single Column](./web-security/sql-injection/lab-10-union-multiple-values-single-column.md) | 🟡 Practitioner | ✅ |
| 11 | [Blind — Conditional Responses](./web-security/sql-injection/lab-11-blind-conditional-responses.md) | 🟡 Practitioner | ✅ |
| 12 | [Blind — Conditional Errors](./web-security/sql-injection/lab-12-blind-conditional-errors.md) | 🟡 Practitioner | ✅ |
| 13 | [Visible Error-Based](./web-security/sql-injection/lab-13-visible-error-based.md) | 🟡 Practitioner | ✅ |
| 14 | [Blind — Time Delays](./web-security/sql-injection/lab-14-blind-time-delays.md) | 🟡 Practitioner | ✅ |
| 15 | [Blind — Time Delays and Information Retrieval](./web-security/sql-injection/lab-15-blind-time-delays-info-retrieval.md) | 🟡 Practitioner | ✅ |
| 16 | [Filter Bypass via XML Encoding](./web-security/sql-injection/lab-16-filter-bypass-xml-encoding.md) | 🟡 Practitioner | ✅ |

### Study platforms

- [PortSwigger Web Security Academy](https://portswigger.net/web-security)

---

```
╔══════════════════════════════════════════════════╗
║         keep learning. keep breaking.            ║
║              keep documenting.                   ║
╚══════════════════════════════════════════════════╝
```

---

> *"The quieter you become, the more you are able to hear."* — Kali Linux
