# Smart Highway Analytics (SHA)

Dashboard web (HTML + CSS + JS) com foco em **tráfego rodoviário, tempo de viagem, segurança viária e visão executiva (OPEX/CAPEX e Receita)** — estruturado para ser **replicável no Power BI** (mesma lógica de filtros → KPIs → visuais).

## 🔗 Dashboard (link fixo)
> Em breve: GitHub Pages (o link permanece o mesmo mesmo após atualizações).

## 🎯 Objetivo
Entregar uma visão **executiva e operacional** para tomada de decisão:
- Identificar variações de volume, receita e tempo de viagem
- Monitorar rotas críticas (congestionamento, incidentes, clima)
- Comparar períodos (hoje/ontem, semana anterior, mês anterior, ano anterior e meta)
- Sustentar ações com um **glossário completo** (definições, cálculos e impactos)

## 🧩 Principais módulos do dashboard
- **Painel Executivo**: KPIs + comparativos + clima + tempo de viagem + status das rotas + gráficos principais
- **Análises por Área**: Operações Tempo Real, Tráfego & Fluxo, Sazonalidade, Receita & Pedágio, OPEX & CAPEX, Segurança Viária, Fontes & APIs, Glossário

## 🧪 Dados (modo demo)
Este repositório usa **dados simulados** para demonstrar:
- Como os filtros se propagam para todos os visuais
- Como a “camada de motor” recalcula KPIs/gráficos a cada interação
- Como a arquitetura se conecta a fontes reais (em fase posterior)

## 🗂️ Estrutura do repositório
- `index.html` → dashboard completo (single file)
- `assets/` → (opcional) imagens, ícones, prints
- `docs/` → (opcional) documentação técnica e decisões de modelagem

## 🚀 Roadmap (próximos passos)
- Publicar via **GitHub Pages** com link fixo
- Evoluir de dados simulados → integração com APIs (clima/rotas) + SQL Server + metas (SharePoint/Excel)
- Padronizar camada de métricas para espelhar DAX/Power BI (medidas e tabelas fato/dim)

## ✅ Como rodar localmente
1. Baixe o repositório
2. Abra o `index.html` no navegador  
   *(ou use VS Code + Live Server para simular refresh)*

## 🧠 Observação
O dashboard foi desenhado com mentalidade de BI:
**KPIs → leitura → hipóteses → testes → recomendação executiva.**
