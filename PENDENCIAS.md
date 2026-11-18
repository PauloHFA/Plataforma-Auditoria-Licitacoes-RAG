# Pendências — Plataforma de Auditoria de Licitações RAG (`Plataforma-Auditoria-Licitacoes-RAG`)

Este documento rastreia o estado técnico do projeto, separando os módulos concluídos e testados das melhorias opcionais e dependências externas de infraestrutura/credenciais.

---

## 1. Concluído e Verificado (Engenharia & Qualidade Offline)
- [x] **Ingestão de Editais & OCR Fallback (`src/ingestion/`):** Extração de PDFs via PyMuPDF e `pdfplumber` (tabelas), fallback automático com Tesseract OCR para PDFs escaneados, validação de arquivos e controle de versões/erratas.
- [x] **Chunking Hierárquico (`src/chunking/`):** Segmentação jurídica por cláusula, item e seção preservando o contexto hierárquico.
- [x] **Busca Híbrida e Reranking (`src/retrieval/`):** Fusão RRF (vetorial + lexical), expansão de sinônimos jurídicos, cache semântico e reclassificação Cross-Encoder com fallback determinístico offline para testes rápidos.
- [x] **Geração Fundamentada e Verificação de Citações (`src/generation/`):** Respostas obrigatoriamente baseadas em evidências (`[Documento, p. N, item X]`), proteção contra *prompt injection* e verificação pós-geração contra alucinações.
- [x] **Auditoria Jurídica & Documentos (`src/audit/`):** Extração de checklist de habilitação (Lei nº 14.133/2021), prazos, penalidades, garantias contratuais, detector de riscos preventivos, gerador de minutas de impugnação e comparativo de versões (Edital vs. Errata).
- [x] **API REST & Interface Web (`src/api/`, `src/ui/`):** Endpoints FastAPI assíncronos e dashboard Streamlit lado a lado com 7 abas.
- [x] **Esteira de Qualidade Total:**
  - `ruff check .` e `ruff format --check .` (100% em conformidade).
  - `mypy src` (0 erros nos 44 arquivos fonte).
  - `pytest --cov=src`: **76/76 testes passando** com **93,9% de cobertura** sem chamadas externas.
  - Pipeline CI configurado em `.github/workflows/ci.yml`.

---

## 2. Melhorias Futuras Opcionais (Backlog Técnico)
- [ ] **Integração com Banco de Jurisprudência TCU:** Indexação automática de acórdãos do TCU para enriquecer as referências dos alertas preventivos.
- [ ] **Exportação PDF/DOCX das Minutas e Dossiê:** Além da exportação em Markdown/CSV já existente, adicionar geração nativa em `.docx` formatado para protocolo administrativo.

---

## 3. Dependências Externas (Infraestrutura & Credenciais de Produção)
- [x] **Modo Testes / Offline:** Todos os testes e validações rodam 100% localmente sem rede, GPU ou chaves externas.
- [ ] **Provedor LLM em Produção (`GROQ_API_KEY` ou Ollama local):** Necessário configurar chave da Groq ou instância Ollama (`qwen2.5:7b`) para inferência LLM real em produção.
- [ ] **PostgreSQL 16 + `pgvector` e Redis (`docker/docker-compose.yml`):** Necessários para persistência vetorial em escala e cache distribuído em ambiente produtivo.
- [ ] **Modelos HuggingFace (`intfloat/multilingual-e5-small`, `BAAI/bge-reranker-v2-m3`):** Baixados sob demanda na primeira execução em produção (`APP_ENV=production`).
