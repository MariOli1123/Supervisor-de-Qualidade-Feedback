# 📊 Supervisor de Qualidade & Feedback para Consultorias (n8n + IA)

Fluxo de trabalho (*workflow*) automatizado em **n8n** focado no monitoramento contínuo de satisfação do cliente (*NPS/CSAT*) para equipes de consultoria. O pipeline extrai respostas de formulários diariamente, realiza cálculos de média de satisfação, filtra desempenhos abaixo da meta e utiliza um **Agente de IA** para gerar e-mails de feedback construtivo com relatórios em Excel anexados.

---

### 📌 Arquitetura do Fluxo

1. **Gatilho Agendado (`Schedule Trigger`):**
   * Execução diária programada (ex: às 09:00 AM) para iniciar a rotina de auditoria e processamento dos dados do dia anterior.

2. **Extração & Iterações de Dados (`Google Sheets` & `Loop Over Items`):**
   * **Consumo de Consultores:** Lê a lista de consultores ativos da empresa.
   * **Iteração em Lote:** Executa um loop item a item para avaliar individualmente o desempenho diário de cada consultor.
   * **Filtragem de Avaliações:** Busca na aba de respostas do formulário todas as avaliações recebidas pelo consultor no dia.

3. **Tratamento, Agregação e Condição (`Edit Fields`, `Summarize` & `If`):**
   * **Tratamento e Normalização:** Mapeia e padroniza campos de avaliação (nota de satisfação, conhecimento técnico, postura e sugestões de melhoria).
   * **Cálculo da Média:** O nó `Summarize` calcula a nota média de satisfação do dia e agrupa o histórico de sugestões textuais dos clientes.
   * **Regra Negocial (Gargalo de Qualidade):** Valida se a média do dia foi **inferior a 7.0**.
     * **Média ≥ 7.0:** O fluxo encerra o loop para aquele consultor (operação bem-sucedida, sem necessidade de intervenção).
     * **Média < 7.0:** O fluxo aciona a esteira de mentoria e geração de relatório.

4. **Sintetização Inteligente via IA (`AI Agent` + `OpenRouter`):**
   * **Modelo LLM:** Conectado via `OpenRouter` (utilizando GPT-4o-mini ou equivalente) com *temperature* ajustada para respostas analíticas.
   * **System Prompt & Roleplay:** A IA atua como **Supervisor de Qualidade**, realizando a síntese dos comentários dos clientes, agrupando pontos de fricção por categorias (ex: atrasos, falta de clareza, proatividade) e gerando o corpo do e-mail em formato **HTML**.

5. **Geração de Anexo & Disparo (`Convert to File` & `Gmail`):**
   * **Compilação do Relatório:** Extrai as linhas brutas do formulário referentes às avaliações daquele consultor e as converte em um arquivo `.xlsx` (Excel).
   * **Envio do E-mail:** Dispara a mensagem personalizada via `Gmail` para o endereço do consultor, contendo a análise da IA no corpo da mensagem e a planilha detalhada em anexo.

---

### 🛠️ Tecnologias & Integrações Utilizadas

* **Orquestração de Processos:** n8n
* **Provedor de LLM / IA:** OpenRouter (`openai/gpt-4o-mini`)
* **Framework de IA:** LangChain (n8n nodes)
* **Manipulação de Dados & Arquivos:** Google Sheets API, Convert to File (XLSX Data Conversion)
* **Comunicação:** Gmail API / OAuth2

---

### 📸 Demonstração Visual

![Fluxo no n8n](https://github.com/MariOli1123/Supervisor-de-Qualidade-Feedback/blob/main/Supervisor%20de%20Qualidade%20&%20Feedback%20para%20Consultorias.png?raw=true))

---

### 🚀 Como Importar e Executar

1. Baixe o arquivo `Automação de Atendimento para Consultoria de Marketing.json` presente neste repositório.
2. No seu ambiente do **n8n**, clique em **Workflows > Import from File** e selecione o arquivo baixado.
3. Configure as credenciais das contas do **Google Sheets**, **Gmail** e **OpenRouter**.
4. Substitua a URL/ID do arquivo do Google Sheets nos nós correspondentes com a estrutura de colunas do seu formulário.
5. Ative o fluxo (*Active: ON*).

---

### 📝 Licença
Este projeto está sob a licença MIT. Veja o arquivo [LICENSE](LICENSE) para mais detalhes.
