# 🛵 Dashboard Operacional de Delivery  Power BI

Projeto de análise de dados construído no Power BI Desktop, simulando a rotina numa operação de delivery

## 📌 Contexto e problema de negócio

Uma plataforma de delivery fictícia precisa acompanhar sua operação em 5 cidades da Baixada Fluminense e Rio de Janeiro. As principais dúvidas do negócio são:

- Qual o volume de pedidos por cidade e como ele evolui ao longo do tempo?
- Qual o tempo médio de entrega e quantos pedidos estão sendo entregues com atraso?
- Como está a performance de cada entregador (tempo de entrega e avaliação do cliente)?
- Qual a taxa de cancelamento de pedidos?

## 🗂️ Sobre os dados

Dataset sintético gerado especificamente para este projeto, com **400 pedidos** distribuídos entre janeiro e junho de 2025, envolvendo 15 entregadores e 15 restaurantes parceiros.

| Tabela | Descrição |
|---|---|
| `Pedidos` | Tabela fato — um registro por pedido (data/hora, cidade, valor, status, tempos, avaliação) |
| `Entregadores` | Dimensão — dados dos entregadores (veículo, cidade base, avaliação média) |
| `Restaurantes` | Dimensão — dados dos restaurantes parceiros (categoria, bairro) |

Os dados brutos foram gerados **intencionalmente com inconsistências** (nomes de cidade escritos de formas diferentes, linhas duplicadas), para simular a realidade de exportações de sistemas legados e permitir a prática de limpeza de dados.

## 🧹 Tratamento de dados (Power Query)

- Padronização da coluna de cidade (`TRIM`, capitalização e substituição de valores divergentes como `"RJ"` → `"Rio De Janeiro"` e `"nova iguacu"` → `"Nova Iguaçu"`)
- Remoção de 5 linhas duplicadas (405 → 400 registros)
- Nulos de `tempo_real_min` e `avaliacao_cliente` **mantidos propositalmente** nos pedidos cancelados, por se tratar de regra de negócio (pedido cancelado não gera tempo de entrega nem avaliação) e não de erro de dado
- Correção do tipo de dado da coluna de data e criação de uma coluna auxiliar (`Data_Pedido`) para granularidade diária nos gráficos de série temporal

## 🔗 Modelagem

Modelo estrela com `Pedidos` como tabela fato, relacionada em "muitos-para-um" com as dimensões `Entregadores` e `Restaurantes` através de `id_entregador` e `id_restaurante`.

## 📐 Medidas DAX

```DAX
Total de Pedidos = COUNTROWS(Pedidos)

Faturamento Total = SUM(Pedidos[valor_pedido])

Tempo Médio de Entrega (min) = AVERAGE(Pedidos[tempo_real_min])

Avaliação Média = AVERAGE(Pedidos[avaliacao_cliente])

Pedidos Atrasados = CALCULATE(COUNTROWS(Pedidos), Pedidos[status_pedido] = "Atrasado")

Pedidos Cancelados = CALCULATE(COUNTROWS(Pedidos), Pedidos[status_pedido] = "Cancelado")

% Pedidos Atrasados = DIVIDE([Pedidos Atrasados], [Total de Pedidos])

% Pedidos Cancelados = DIVIDE([Pedidos Cancelados], [Total de Pedidos])
```

## 📊 Dashboard

O painel reúne:

- **Cartões de KPI**: total de pedidos, tempo médio de entrega, % de pedidos atrasados e faturamento total
- **Total de pedidos por cidade** (gráfico de barras)
- **Evolução de pedidos ao longo do tempo** (gráfico de linha)
- **Ranking de entregadores**: total de pedidos, tempo médio de entrega e avaliação média por entregador
- **Filtros interativos** (segmentações) por status do pedido e por cidade

> o print está em files para conhecer o dashboard

## 💡 Principais insights

- A cidade de Duque de Caxias concentra o maior volume de pedidos da operação
- A taxa geral de pedidos atrasados fica em torno de 11%
- Entregadores com tempo médio de entrega mais alto tendem a apresentar avaliação do cliente mais baixa

## 🛠️ Ferramentas utilizadas

- Power BI Desktop (Power Query, modelagem de dados, DAX, visualização)
- Excel (fonte de dados bruta)

## 📁 Estrutura do repositório

```
├── dashboard-delivery.pbix
├── delivery_dataset_bruto.xlsx
e Print dashboard.png
└── README.md
```
