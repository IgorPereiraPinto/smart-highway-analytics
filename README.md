# Smart Highway Analytics (SHA) — v8.0

> Plataforma de Business Intelligence para monitoramento operacional e gestão executiva de concessões rodoviárias — projetada como protótipo de alta fidelidade para implementação no Power BI.

![Stack](https://img.shields.io/badge/Stack-HTML%20%2B%20Chart.js-8DC63F?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Demo%20%2F%20Protótipo-F7941D?style=for-the-badge)
![Versão](https://img.shields.io/badge/Versão-8.0-006838?style=for-the-badge)

<br>

### 🚀 [Clique aqui para abrir o dashboard →](https://igorpereirapinto.github.io/smart-highway-analytics/)

> ⚡ Abre direto no navegador — sem instalação, sem login.

---

## 📌 Contexto do problema

Concessões rodoviárias de grande porte — como a **Ecovias Imigrantes (SP-150/SP-160), Raposo Tavares (SP-270) e Castelo Branco (SP-280)** — geram diariamente centenas de milhares de transações de pedágio, dados de contagem veicular por pórtico, ocorrências de segurança e indicadores financeiros distribuídos em múltiplos sistemas (SQL Server, SharePoint, APIs externas).

**O problema:** esses dados chegavam fragmentados, sem visão consolidada e sem capacidade de comparação entre períodos, concessões ou segmentos de corredor. Decisões operacionais dependiam de relatórios manuais em Excel com defasagem de horas.

**O que o SHA resolve:**

| Pergunta de negócio | Como o dashboard responde |
|---|---|
| O desempenho hoje está acima ou abaixo do planejado? | Faixa comparativa com 5 modos de referência (ontem / semana / mês / ano / meta) |
| Onde está o gargalo operacional agora? | Tabela de rotas com status em tempo quase real + perfil de velocidade por KM |
| Qual a causa provável do congestionamento? | Fatores explicáveis por rota: clima, volume, incidente, sazonalidade |
| Estamos dentro do orçamento? Qual categoria está estourando? | OPEX por driver vs. orçado + desvio em R$ por categoria |
| O plano de investimentos está sendo executado? | CAPEX executado vs. planejado por projeto + alerta de subexecução |
| A meta de segurança regulatória (ANTT ≤ 0,40 acid./M VKM) está em risco? | Taxa de acidentalidade por período + hotspot por segmento de KM |

---

## 🎯 Objetivo do projeto

Demonstrar um pipeline analítico completo — da pergunta de negócio ao insight executivo — com foco em:

1. **Governança de dados:** motor único de estado (`state`) como single source of truth, com todos os filtros sincronizados
2. **Modelagem de KPIs:** metas por concessão × período, multiplicadores por corredor e sentido, comparativos coerentes
3. **Prototipagem de BI:** estrutura de dados e lógica de cálculo prontas para replicação no Power BI (DirectQuery + Import Mode)
4. **Comunicação com stakeholders:** linguagem executiva nos cards, alertas acionáveis e glossário com fórmulas, leituras e ações recomendadas

---

## 🏗️ Arquitetura e decisões técnicas

### Por que HTML single-file e não Power BI diretamente?

O protótipo em HTML permite iterar na lógica de filtros, cálculo de KPIs e layout sem dependência de licença Power BI, gateway de dados ou fontes conectadas. A estrutura JavaScript espelha intencionalmente a arquitetura DAX/Power Query que será usada na implementação final.

### Motor de filtros (Single Source of Truth)

```
state = { concessao, corredor, period, sentido, cmp }
         ↓
   renderAll()
         ↓
┌────────────────────────────────────────────────┐
│ getKPIs()    → cards VDM / Rec / Vel / Ac / OPEX / CAPEX │
│ getDeltas()  → faixa comparativa (5 modos)               │
│ filterRoutes()→ tabela de rotas (concessão + corredor)    │
│ renderPage() → todos os gráficos da view ativa           │
└────────────────────────────────────────────────┘
```

Qualquer mudança em qualquer filtro dispara `renderAll()` — garantindo que nenhum visual fique dessincronizado.

### Estrutura de metas

```javascript
META[concessao][periodo] = { vdm, rec, vel, ac, opex, capex }
```

Metas definidas para 4 concessões × 4 períodos = 16 combinações. O modo "Comparar com → Meta" usa esta tabela como baseline, com deltas coloridos (verde/laranja/vermelho) por KPI.

### Arquitetura de dados recomendada para Power BI

```
Fontes                  Modo           Tabelas
──────────────────────────────────────────────────
SQL Server (pedágio)  → Import + IR  → fato_transacoes
SQL Server (contagens)→ Import + IR  → fato_contagens
SQL Server (financeiro)→ Import + IR → fato_financeiro
OpenWeatherMap API    → DirectQuery  → fato_clima_rt
Google Maps API       → DirectQuery  → fato_rotas_rt
SharePoint/Excel      → Import       → dim_metas, dim_orcamento

Chave padrão: SK_data (YYYYMMDD) × SK_praca (INT) × SK_via (INT)
IR = Incremental Refresh (watermark: LastModified)
```

---

## 🧩 Estrutura do dashboard

### Painel Executivo (one-page)
Visão consolidada para reuniões de diretoria. Todos os elementos recalculam com os filtros.

- **6 KPIs dinâmicos:** VDM · Receita · Velocidade Média · Acidentes · OPEX% · CAPEX%
- **Faixa comparativa:** deltas coloridos vs. 5 referências
- **Card de clima:** OpenWeather demo — 3 pontos (KM 20 / Serra / Cubatão)
- **Tempo de viagem:** 14 pares O-D com fatores explicáveis de impacto
- **Status das rotas:** 12 rotas filtradas por concessão + corredor + sentido
- **Gráficos:** volume hora a hora · forecast 30 dias (Prophet) · mix de pagamento · comparativo de volume

### Análises por Área (9 abas)

| Aba | Foco | KPIs dinâmicos | Gráficos |
|---|---|---|---|
| 🏆 Executivo | Visão consolidada p/ diretoria | VDM, Receita, Acidentes, OPEX, CAPEX | Volume 6M + Receita · Ranking Praças · Forecast · Mix Pagamento |
| ⚙️ Operações | Tempo real — rotas e velocidade | Cong. médio · Rotas críticas | Tabela completa · Perfil velocidade KM · Congestionamento 24h |
| 🚗 Tráfego & Fluxo | Volume horário e padrões | VDM · Baixada · Capital · VKT | Hora a hora vs média · Composição frota · Baixada vs Capital |
| 📅 Sazonalidade | Padrões e eventos críticos | Feriado +68% · Pico verão +82% | Dia da semana · Índice sazonal · Impacto climático · Calendário |
| 💰 Receita | Arrecadação e ticket médio | Receita · Ticket · TAG · Isenções | Receita vs Meta 6M · Por praça · Por categoria · Evolução TAG |
| 📋 OPEX & CAPEX | Orçamento e investimentos | OPEX% · Desvio R$ · CAPEX% | Por categoria · Por projeto · Acumulado 6M · Alertas desvio |
| 🦺 Segurança | Acidentes e conformidade ANTT | Ac./mês · Graves · Taxa/M VKM · TMI | Por hora · Hotspot KM · Por clima · Tendência 6M |
| 🔌 Fontes & APIs | Arquitetura e governança | 4/4 fontes · ETL 5/8 · QS 97,3% | Pipeline cronograma · Quality gates · Arquitetura Power BI |
| 📖 Glossário | Referência para stakeholders | — | 30 indicadores com definição, cálculo, leitura executiva e ação |

---

## 📊 Fontes de dados (modo demo → produção)

O dashboard usa **dados simulados determinísticos por filtro (seed)** — o mesmo filtro sempre produz o mesmo resultado, garantindo estabilidade para validação da lógica de cálculo.

Em produção, cada fonte tem protocolo definido:

| Fonte | Protocolo | Cadência | Power BI |
|---|---|---|---|
| **OpenWeatherMap** | REST API JSON | 15 min (clima) / 1h (forecast) | DirectQuery |
| **Google Maps Distance Matrix** | REST API JSON | 15 min contínuo | DirectQuery |
| **SQL Server** | ODBC/JDBC | Tempo real → ETL incremental 8×/dia | Import + Incremental Refresh |
| **SharePoint / Excel** | Microsoft Graph API | Manual + ETL diário | Import |

### Quality Gates implementados

- ✅ Nulos em colunas obrigatórias: 0 / 850k registros
- ✅ Duplicidade SK_data × SK_praca: 0
- ✅ Reconciliação receita vs transações: Δ R$ 0,00
- ⚠️ Outliers (> 3σ): 2 registros em revisão manual
- ⚠️ Lacuna temporal: 1 gap de 20 min (reinicialização de pórtico)
- ❌ Completude SharePoint: 2 linhas em branco (Orcamento_SHA.xlsx B12:B13)

---

## 🔑 Glossário — metodologia de construção

Cada um dos 30 indicadores no dashboard foi documentado com 6 campos:

1. **Definição:** o que é o indicador
2. **Cálculo:** fórmula explícita (replicável em DAX/SQL)
3. **Leitura executiva:** o que significa subir ou cair
4. **Principais drivers:** o que afeta o indicador
5. **Pergunta de negócio:** qual decisão este KPI suporta
6. **Ação recomendada:** o que o gestor deve fazer com o resultado

Esta estrutura segue a metodologia **"pergunta → dado → insight → ação"** — base do trabalho de BI orientado a resultado.

---

## 🚀 Como usar

```bash
# Clone o repositório
git clone https://github.com/igorpereirapinto/smart-highway-analytics.git

# Abra no navegador
open index.html

# Ou com Live Server (VS Code) para simular refresh automático
```

Não há dependências externas além de Chart.js (carregado via CDN) e Google Fonts. Funciona offline após o primeiro carregamento.

---

## 🗂️ Estrutura do repositório

```
smart-highway-analytics/
├── index.html          # Dashboard completo (single file)
├── README.md           # Este arquivo
└── assets/             # (opcional) prints e documentação visual
```

---

## 🛠️ Stack

| Tecnologia | Uso |
|---|---|
| HTML5 / CSS3 | Interface e layout responsivo |
| JavaScript (Vanilla) | Motor de filtros, cálculo de KPIs, renderização |
| Chart.js 4.4.1 | 22+ gráficos (line, bar, doughnut, polar area) |
| Google Fonts (Sora + JetBrains Mono) | Tipografia |
| GitHub Pages | Hospedagem estática |

---

## 🔜 Próximos passos

- [ ] Integração real com OpenWeatherMap API (chave de produção)
- [ ] Integração real com Google Maps Distance Matrix API
- [ ] Conexão com SQL Server via API intermediária (Node.js / FastAPI)
- [ ] Migração para Power BI com a mesma lógica de filtros e KPIs
- [ ] Adicionar autenticação (Azure AD) para acesso corporativo

---

## 👤 Autor

**Igor Pereira Pinto**  
Analista de Dados / BI Sênior  
[LinkedIn](https://linkedin.com/in/igorpereirapinto) · [GitHub](https://github.com/igorpereirapinto)

> *"Dado sem pergunta de negócio é relatório. Dado com pergunta é decisão."*



