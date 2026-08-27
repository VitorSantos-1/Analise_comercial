# Painel Executivo Comercial & Controle de Vendas

Relatório executivo desenvolvido em **Power BI** para o acompanhamento de vendas e da
performance comercial de uma rede varejista com múltiplas lojas. O painel consolida, em uma
única visão atualizada diariamente, as informações de vendas, metas e cadastros que costumam
ficar espalhadas em planilhas e sistemas distintos, apoiando decisões de curto prazo do time
comercial e da diretoria.

---

## Visão Geral

A solução centraliza a base de vendas, o cadastro de metas e o cadastro de filiais em um modelo
de dados único e entrega um relatório executivo com navegação por guias analíticas. O objetivo é
responder, de forma rápida e confiável, às perguntas que a operação comercial faz todos os dias:
estamos no ritmo de bater a meta deste mês? quais lojas estão à frente e quais estão atrasadas?
qual comprador puxa ou trava o resultado? como estamos frente ao mesmo período do ano passado?

> **Nota de confidencialidade:** todos os dados exibidos no arquivo `.pbix` são fictícios e
> anonimizados, criados exclusivamente para demonstração de portfólio.

---

## Contexto de Negócio

No varejo de múltiplas lojas, o resultado comercial se constrói dia a dia, mas a informação
raramente chega consolidada em tempo hábil. Quando a apuração de metas só acontece no fechamento
do mês, já não há espaço para reação: a meta perdida vira prejuízo consolidado. Além disso,
comparar o desempenho de lojas e compradores por percepção gera injustiça e discussão — falta um
denominador comum.

Este painel ataca exatamente esse ponto. Ele transforma a base transacional de vendas em um
instrumento de gestão diária, no qual a meta deixa de ser um número estático de fim de mês e passa
a ser acompanhada em relação ao realizado acumulado e à projeção de fechamento (Run Rate).

## O Problema que Resolve

- **Ausência de visão consolidada e diária** de vendas versus meta por loja e por comprador.
- **Reação tardia:** metas acompanhadas apenas no fechamento, quando não há mais como corrigir o mês.
- **Comparação injusta** entre unidades e compradores, por falta de indicadores padronizados.
- **Retrabalho** na montagem manual de relatórios comerciais em planilhas.

## Público e Decisões Apoiadas

- **Diretoria comercial:** enxerga o ritmo do mês e onde intervir para proteger o resultado.
- **Gerentes de loja:** acompanham a própria meta e se posicionam no ranking da rede.
- **Compradores:** medem a performance da sua categoria e priorizam ações comerciais.

## Impacto e Valor Gerado

Deu à diretoria e aos compradores visão diária de meta versus realizado e projeção de fechamento,
permitindo corrigir o mês antes de perdê-lo e cobrar performance por loja e por comprador com base
em dado padronizado, não em percepção. Reduz o tempo gasto na montagem manual de relatórios e
elimina divergências entre versões de planilha.

---

## Arquitetura de Dados e Abordagem Técnica

### Fontes de dados
- Base transacional de **vendas** (faturamento por loja, período e comprador/categoria).
- Cadastro de **metas** comerciais por filial e período.
- Cadastro de **filiais/lojas**.

### Transformação (Power Query / M)
Ingestão e padronização das bases, tratamento de tipos e datas, unificação de cadastros e criação
das chaves de relacionamento entre vendas, metas e filiais. A limpeza é feita na camada de consulta
(Power Query), mantendo o modelo enxuto e performático.

### Modelagem dimensional (Star Schema)
Modelo em estrela para desacoplar fatos de dimensões e garantir performance e clareza analítica:

- **Fato Vendas** — grão de venda por dia / loja / categoria.
- **Fato Metas** — meta por período e filial.
- **Dimensão Loja** — atributos de unidade (região, porte, etc.).
- **Dimensão Calendário** — tabela de datas dedicada, base das análises temporais (YoY / MoM).

### Principais medidas (DAX) — lógica implementada

```DAX
Faturamento Bruto   = SUM( FatoVendas[Valor] )
Meta do Periodo     = SUM( FatoMetas[Meta] )
% Realizado         = DIVIDE( [Faturamento Bruto], [Meta do Periodo] )
Margem Bruta %      = DIVIDE( [Lucro Bruto], [Faturamento Bruto] )
Dias Decorridos     = COUNTROWS( FILTER( Calendario, Calendario[Data] <= MAX( Calendario[Data] ) ) )
Run Rate (Projecao) = DIVIDE( [Faturamento Bruto], [Dias Decorridos] ) * [Dias no Mes]
Variacao YoY %      = DIVIDE( [Faturamento Bruto] - [Faturamento Ano Anterior], [Faturamento Ano Anterior] )
```

> As fórmulas acima descrevem a lógica de cálculo do relatório e servem como referência de leitura do modelo.

---

## Indicadores e Análises (KPIs)

- **Faturamento Bruto & Alcance de Meta (% Realizado):** acompanhamento diário da meta por filial.
- **Margem Comercial Bruta:** rentabilidade percentual sobre as vendas acumuladas no período.
- **Projeção de Faturamento (Run Rate):** estimativa de fechamento do mês pela velocidade média de vendas.
- **Ranking de Lojas & Compradores:** comparativo padronizado de desempenho por unidade e categoria.
- **Comparativos temporais (YoY / MoM):** evolução frente ao ano anterior e ao mês anterior.

## Estrutura de Páginas do Relatório

1. **Visão Executiva** — indicadores-chave, % de meta e projeção do mês.
2. **Desempenho por Loja** — ranking e detalhamento por unidade.
3. **Desempenho por Comprador / Categoria** — contribuição de cada categoria ao resultado.
4. **Análise Temporal** — séries históricas e comparativos YoY / MoM.

## Como Visualizar

O relatório é apresentado pelas imagens da seção **Prints** abaixo, que reproduzem as páginas
principais do painel. O arquivo-fonte (`.pbix`) não é versionado neste repositório por conter o
modelo de dados; pode ser disponibilizado sob solicitação para avaliação técnica.

## Estrutura de Arquivos

```text
README.md    -> Documentacao do projeto (contexto de negocio + arquitetura tecnica)
docs/        -> Prints das paginas do relatorio
.gitignore   -> Exclui binarios (.pbix) e planilhas de trabalho do versionamento
```

## Prints

As imagens abaixo apresentam as páginas principais do relatório — a imagem é o que comunica o
projeto de forma imediata. Os prints serão adicionados na pasta `docs/`.

<!-- Descomente ao adicionar a imagem:
![Visao executiva do painel comercial](docs/print-1.png)
-->

## Autor

Vitor Santos — Análise de Dados e Inteligência Comercial (Varejo).
