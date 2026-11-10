# 🛡️ AppSec Lab: OWASP Juice Shop — Provas de Conceito (PoCs) & Análise de Vulnerabilidades

<div align="center">

![Application Security](https://img.shields.io/badge/Security-AppSec%20%7C%20DevSecOps-red?style=for-the-badge&logo=shield)
![OWASP Top 10](https://img.shields.io/badge/OWASP-Top%2010%20Study-blue?style=for-the-badge&logo=owasp)
![Target](https://img.shields.io/badge/Target-OWASP%20Juice%20Shop-orange?style=for-the-badge&logo=docker)
![Status](https://img.shields.io/badge/Status-Completed-success?style=for-the-badge)

<p align="center">
  <b>Repositório prático de treinamento em Segurança de Aplicações (Application Security / AppSec)</b><br>
  Análise de causa raiz, exploração prática de vulnerabilidades, provas de conceito (PoCs) e remediação estática de código (*Find It & Fix It*).
</p>

---

**Pesquisador / Desenvolvedor:** [Nicolas Matheus Lima Oliveira](https://github.com/nicolas-mtheus)  
**Ambiente de Testes:** OWASP Juice Shop (`v20.3.0`) via Docker Container  
**Data:** Novembro de 2026  

---

</div>

## 📌 Sumário Executivo

Este repositório documenta laboratórios práticos de **Application Security (AppSec)** conduzidos na aplicação intencionalmente vulnerável **OWASP Juice Shop**. 

Cada estudo vai além da exploração convencional (Pentest/CTF), cobrindo:
1. **Identificação e Mapeamento:** Enquadramento no **OWASP Top 10** e classificação **CWE** (*Common Weakness Enumeration*).
2. **Exploração e PoC Técnica:** Vetor de ataque com carga maliciosa (*payload*), etapas de reprodução e evidências em imagem.
3. **Engenharia Reversa de Código:** Inspeção do código-fonte (Angular / Express / TypeScript) e análise do desafio interativo de correção (*Find It* e *Fix It*).
4. **Remediação & Mitigação:** Implementação de defesas em profundidade, arquitetura segura e prevenção para ambientes de produção.

---

## 🎯 Matriz de Vulnerabilidades & PoCs

| # | Desafio / Cenário | Categoria OWASP | CWE Associada | Prova de Conceito |
|---|---|---|---|:---:|
| 01 | **Score Board** | `A01:2021` – Broken Access Control<br>`A05:2021` – Security Misconfiguration | **CWE-200** / **CWE-284** / **CWE-656** | [📄 Ler PoC](PoC_ScoreBoard.md) |
| 02 | **DOM XSS** | `A03:2021` – Injection<br>*(Legacy: A7:2017 – XSS)* | **CWE-79** / **CWE-116** | [📄 Ler PoC](PoC_DOMXSS.md) |
| 03 | **Bonus Payload** | `A03:2021` – Injection / Frame Injection | **CWE-79** / **CWE-1021** | [📄 Ler PoC](PoC_BonusPayload.md) |
| 04 | **Confidential Document** | `A01:2021` – Broken Access Control<br>`A05:2021` – Security Misconfiguration | **CWE-548** / **CWE-200** / **CWE-219** | [📄 Ler PoC](PoC_ConfidentialDocument.md) |

---

## 🔍 Detalhamento dos Achados

### 1. [Score Board — Quebra de Controle de Acesso e Segurança por Obscuridade](PoC_ScoreBoard.md)
* **Vetor:** Rotas Angular expostas no front-end (`/#/score-board`) sem guards de autenticação/autorização (`canActivate`).
* **Conceito Chave:** *Security through Obscurity* não é controle de segurança. Ocultar botões na UI não impede o acesso direto ao recurso.
* **Solução:** Implementação de controle de acesso baseado em papéis (RBAC) e validação obrigatória no servidor (*backend-enforced authorization*).

### 2. [DOM XSS — Desativação Insegura de Sanitização (DomSanitizer Bypass)](PoC_DOMXSS.md)
* **Vetor:** Uso de `bypassSecurityTrustHtml(queryParam)` no componente de busca.
* **Carga Maliciosa:** `<iframe src="javascript:alert(`xss`)">`
* **Conceito Chave:** Desativação voluntária dos mecanismos nativos de higienização do framework cliente.
* **Solução:** Eliminação do bypass de segurança, confiando na interpolação e no escape automático do template engine (`{{ queryParam }}`).

### 3. [Bonus Payload — Injeção de Conteúdo e Frames Arbitrários](PoC_BonusPayload.md)
* **Vetor:** Injeção de `<iframe>` interativo de terceiros com reprodução automática (*SoundCloud Player*) via manipulação do DOM.
* **Conceito Chave:** Impacto além do `alert()`: defacement de interface, phishing via frames e violação de integridade visual.
* **Solução:** Política de Segurança de Conteúdo rigorosa (**CSP** com diretiva `frame-src 'none'` ou allowlist restrita) combinada com sanitização estrita via bibliotecas como DOMPurify.

### 4. [Confidential Document — Listagem Indevida de Diretórios & Exposição de Dados](PoC_ConfidentialDocument.md)
* **Vetor:** Middleware `serveIndex` exposto na rota pública `/ftp`, permitindo download de documentos estratégicos confidenciais (`acquisitions.md`).
* **Conceito Chave:** Exposição de arquivos sensíveis e backups na raiz web acessível (*Web Root*).
* **Solução:** Desativação total da indexação de diretórios (`serveIndex`), remoção de arquivos confidenciais do diretório público e proteção de download com controle de sessão.

---

## 🏗️ Estrutura do Repositório

```text
├── assets/
│   ├── ScoreBoard/             # Evidências do acesso, Find It e Fix It
│   ├── DOMXSS/                 # Evidências de execução de alert e correções
│   ├── BonusPayload/           # Evidências de injeção de iframe e áudio
│   └── ConfidentialDocument/   # Evidências de navegação no /ftp e documento vazado
├── PoC_ScoreBoard.md           # PoC Completa: Score Board
├── PoC_DOMXSS.md               # PoC Completa: DOM XSS
├── PoC_BonusPayload.md         # PoC Completa: Bonus Payload
├── PoC_ConfidentialDocument.md # PoC Completa: Confidential Document
└── README.md                   # Documentação geral e índice de estudos
```

---

## 🚀 Como Subir o Ambiente de Testes Localmente

Para replicar os testes executados nestas PoCs utilizando o Docker:

```bash
# 1. Baixar a imagem estável do Juice Shop
docker pull bkimminich/juice-shop

# 2. Executar o container mapeando a porta 3000
docker run -d -p 127.0.0.1:3000:3000 --name juice-shop bkimminich/juice-shop

# 3. Acessar a aplicação no navegador
http://localhost:3000
```

---

<div align="center">

Feito com dedicação aos estudos de **Segurança Ofensiva**, **Defensiva** e **Engenharia de Software Segura (DevSecOps)**.

</div>
