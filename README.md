# Projeto de Análise Comercial e BI (Power BI & DAX)

Este repositório contém um projeto de Business Intelligence desenvolvido para análise de desempenho comercial, comportamento de mix de produtos e auditoria de políticas de desconto aplicada a pequenos negócios do setor de comércio e serviços.

---

## 📊 Visão Geral do Dashboard
* **Faturamento Bruto Total:** R$ 15.096,00
* **Faturamento Líquido Total:** R$ 14.571,05
* **Volume Total Comercializado:** 908 unidades
* **Taxa Efetiva de Desconto Geral:** 3,48%
* **Preço Médio Realizado por Unidade:** R$ 16,05

---

## 1. Problema de Negócio
Pequenos comércios frequentemente enfrentam dificuldades para identificar quais produtos geram faturamento real versus quais consomem margem de lucro por meio de descontos descontrolados. O objetivo deste projeto foi auditar uma base transacional para responder a perguntas críticas de gestão:
* *Quais produtos lideram a receita e quais lideram o volume de vendas?*
* *O crescimento do volume em março foi impulsionado por queima de preços (descontos agressivos) ou manutenção do valor real?*
* *Qual produto concentra o maior esforço de abatimento comercial e qual é o risco financeiro associado?*

---

## 2. Preparação e Limpeza de Dados (ETL)
A base inicial continha inconsistências comuns em dados operacionais de pequenas empresas. O tratamento foi realizado no **Power Query (Linguagem M)**:
* **Remoção de Colunas Inexistentes/Lixo:** Exclusão de colunas vazias (`Column11` e `Column12`).
* **Normalização de Textos:** Padronização de maiúsculas/minúsculas e remoção de espaços em branco nas colunas de `Produto` e `Regiao`.
* **Auditoria de Linhas Duplicadas:** Identificação e remoção de registros duplicados, consolidando exatamente **49 registros válidos** no trimestre (Qtr 1 de 2026).
* **Parametrização da Fonte:** Implementação de parâmetro de caminho (`CaminhoArquivo`) para garantir a reprodutibilidade do projeto em qualquer ambiente local ou no GitHub.

---

## 3. Dicionário de Dados
A tabela principal `base_vendas_inicial` é composta pelas seguintes dimensões e métricas:
* `Data`: Data da transação (tipo Data).
* `Produto`: Nome do item comercializado (Mochila, Garrafa, Caderno, Estojo, Caneta Azul).
* `Categoria`: Classificação mercadológica (Papelaria, Acessórios).
* `Regiao`: Localização geográfica da venda (Sudeste, Nordeste, Centro-Oeste, Norte, Sul).
* `Quantidade`: Volume de unidades comercializadas por transação.
* `Preco_Unitario`: Preço de tabela unitário.
* `Desconto`: Alíquota percentual de abatimento concedida.
* `Faturamento bruto`: Receita potencial de tabela (`Quantidade * Preço_Unitario`).
* `Valor de desconto`: Montante financeiro absoluto abatido (`Faturamento bruto * Desconto`).
* `Faturamento Líquido`: Receita efetiva realizada (`Faturamento bruto - Valor de desconto`).

---

## 4. Arquitetura do Modelo e Medidas DAX
O modelo utiliza o esquema estrela simplificado e medidas DAX rigorosamente validadas:

### A. Preço Médio Unitário (Ponderado por Volume)
Calcula a receita líquida média por unidade vendida, isolando o impacto do mix:
```dax
Preço Médio Unitário = DIVIDE(SUM('base_vendas_inicial'[Faturamento Líquido]), SUM('base_vendas_inicial'[Quantidade]))
B. Taxa de Desconto Efetiva
Mede o percentual real de abatimento concedido sobre o faturamento bruto da loja:
Taxa de Desconto = DIVIDE(SUM('base_vendas_inicial'[Valor de desconto]), SUM('base_vendas_inicial'[Faturamento bruto]), 0)
C. Participação Regional no Faturamento Líquido
Calcula o peso relativo de cada região sobre a receita total da empresa:
Participação Regional = DIVIDE(SUM('base_vendas_inicial'[Faturamento Líquido]), CALCULATE(SUM('base_vendas_inicial'[Faturamento Líquido]), ALL('base_vendas_inicial'[Regiao])))
Participação Regional = DIVIDE(SUM('base_vendas_inicial'[Faturamento Líquido]), CALCULATE(SUM('base_vendas_inicial'[Faturamento Líquido]), ALL('base_vendas_inicial'[Regiao])))
5. Resultados Executivos e Insights de Negócios
A. A Polarização do Mix: Faturamento vs. Volume
Mochila: Líder absoluta em faturamento líquido (R$ 5.094,00 / 34,96% do total), apesar de representar apenas 4,96% das unidades (45 unidades no trimestre). Possui o maior preço médio unitário (R$ 114,29 em março).

Caneta Azul: Líder absoluta em volume (480 unidades, 52,86% do total), mas com menor representatividade em receita (R$ 1.169,75, 8,03% do total) devido ao baixo preço unitário (R$ 2,44).

B. O Comportamento de Março (Fato vs. Hipótese)
Hipótese Refutada: A explosão de vendas em março (que saltou para 401 unidades frente a 225 em janeiro e 282 em fevereiro) não foi gerada por queima generalizada de preços. 4 dos 5 produtos mantiveram ou elevaram levemente o preço médio realizado no mês.

C. Auditoria Crítica de Descontos
A Mochila concentrou 5,67% de taxa de desconto efetiva, respondendo por 58,29% de todo o dinheiro de desconto concedido pela empresa no trimestre (R$ 306,00 de um total de R$ 524,95).

Recomendação de Gestão: Identificou-se que a Mochila recebeu descontos de até 15% em transações de baixo volume, evidenciando falha de alçada de desconto no balcão sem contrapartida de volume de compras.

6. Limitações Técnicas do Modelo Atual
Ausência de Custos (CMV): A base não dispõe de custo unitário de aquisição, impedindo no momento o cálculo de Margem de Contribuição e Lucro Líquido por produto.

Ausência de Identificadores (ID de Pedido/Cliente): Impede a mensuração precisa de Ticket Médio por transação isolada e Lifetime Value (LTV) de clientes.

7. 🛠️ Competências Aplicadas
Engenharia e Limpeza de Dados: Power Query, Linguagem M, Parametrização de Fontes, Tratamento de Nulos e Deduplicação.

Modelagem e DAX: Medidas Agregadas Ponderadas (DIVIDE, SUM), Indicadores de Rentabilidade e Auditoria de Descontos.

Data Storytelling & BI: Design de Relatórios Executivos, Cartões de KPI e Gráficos de Comportamento Comercial.

Visão de Negócios: Análise de Mix de Produtos (Curva ABC de Faturamento vs. Volume) e Governança de Alçadas de Desconto.

⚙️ Pré-requisitos e Instalação
Power BI Desktop (versão atualizada para suporte a DAX avançado e parâmetros do Power Query).

Microsoft Excel (para inspecionar a base de dados em data/Resumo_Produtos.xlsx).

👤 Autor
Bruno Guimarães

Atuação: Analista de Dados & Especialista em Soluções de BI para Pequenos Negócios e Turismo.
LinkedIn:https://www.linkedin.com/in/bruno-marcio-de-jesus-guimar%C3%A3es-460247360/
Email:guimaraesbruno78@gmail.com
