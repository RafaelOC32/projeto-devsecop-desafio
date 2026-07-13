# Desafio DevSecOps — Gerenciador de Tarefas

## Sobre o Projeto
Este repositório faz parte do desafio prático do módulo de DevSecOps da ADA Tech.
Este projeto estava com vulnerabilidades propositais e uma pipeline incompleta.
Meu objetivo foi de **implementar a pipeline de segurança** e **corrigir as vulnerabilidades**.

## Estado atual
A pipeline está **completa**. Os steps de segurança estão implementados na pipeline, os erros apontados por esses steps de segurança foram corrigidos e a pipeline **finaliza** com tudo verde. Tudo documentado neste README.md.

## Minha missão
1. Implementar os steps de segurança no `pipeline.yml`
2. Fazer a pipeline **quebrar** ao detectar os problemas
3. Corrigir as vulnerabilidades encontradas
4. Fazer a pipeline **passar** com tudo verde ✅
5. Documentar o funcionamento da pipeline neste README

## O que foi implementado
- [X] Secrets Scanning com **Gitleaks**
- [X] SAST com **Semgrep**
- [X] SCA com **Grype**
- [X] Deploy com **GitHub Pages**

## Como a pipeline funciona
### **1. Steps de segurança configurados**
A pipeline foi configurada com os seguintes steps de segurança:
- **Gitleaks (Secrets Scanning)** → detecta chaves e credenciais expostas no código.
- **Semgrep (SAST)** → análise estática para encontrar vulnerabilidades no código fonte.
- **Grype (SCA)** → análise de dependências e bibliotecas para identificar vulnerabilidades conhecidas.

---

### **2. Erros dectados**
Durante a execução inicial da pipeline, foram encontrados os seguintes problemas:

#### **Gitleaks**
O Gitleaks identificou credenciais sensíveis hardcoded no código:

1. **Token de API**
   - **Arquivo:** `src/script.js`
   - **Trecho:** `const API_KEY = "..."`  
   - **Risco:** Exposição de token de acesso do GitHub, permitindo uso indevido da conta ou repositório.

2. **Senha de Banco de Dados**
   - **Arquivo:** `src/script.js`
   - **Trecho:** `const DB_PASSWORD = "...";`  
   - **Risco:** Exposição de senha de ambiente de produção, comprometendo a segurança do banco de dados.

---

#### **Semgrep**
- **Arquivo:** `src/script.js`
- **Problema:** Uso de `eval()` para executar código dinâmico.
- **Risco:** Possível vulnerabilidade de *code injection*.

---

#### **Grype**
- Nenhuma vulnerabilidade encontrada nas dependências do projeto.

---

### **3. Correções realizadas**
#### **Gitleaks**
- **Remoção de credenciais hardcoded**  
  - Substituídas por variáveis de ambiente (`process.env.API_KEY` e `process.env.DB_PASSWORD`).
- **Configuração de Secrets no GitHub Actions**  
  - Criados secrets `API_KEY` e `DB_PASSWORD` no repositório.  
  - Referenciados no workflow com:
    ```yaml
    env:
      API_KEY: ${{ secrets.API_KEY }}
      DB_PASSWORD: ${{ secrets.DB_PASSWORD }}
    ```
- **Revogação de credenciais expostas**  
  - Token GitHub revogado e regenerado.  
  - Senha de banco substituída por nova credencial segura.  
- **Garantia de não versionamento**  
  - Adicionado `.env` ao `.gitignore` para evitar exposição futura de credenciais locais.

---

#### **Semgrep**
  - Substituído uso de `eval()` por chamada direta ao `console.log`:
    ```js
    console.log("Tarefa adicionada: " + input.value);
    ```
  - Eliminado risco de execução dinâmica de código.

---

#### **Grype**
  - Nenhuma ação necessária, pois não foram encontradas vulnerabilidades.

---

### **4. Resultado**
Após as correções:
- O **Gitleaks** passou sem detectar segredos expostos.
- O **Semgrep** rodou 217 regras em 4 arquivos e não encontrou vulnerabilidades.
- O **Grype** confirmou ausência de falhas nas dependências.
- Pipeline finalizou com sucesso ✅

## URL de Produção
- URL do Repositório:
https://github.com/RafaelOC32/projeto-devsecop-desafio
- URL GitHub Pages: https://rafaeloc32.github.io/projeto-devsecop-desafio/
