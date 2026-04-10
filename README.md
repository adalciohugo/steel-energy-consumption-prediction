# Previsão de Consumo Energético Industrial

## O problema

Indústrias que consomem muita energia elétrica pagam tarifas muito diferentes dependendo do horário. Das 17h30 às 20h30 — o chamado horário de ponta — a tarifa pode ser até **3 vezes mais cara** do que nos outros períodos. Se a fábrica consegue deslocar parte da produção para fora desse horário, a economia é significativa.

O problema é que isso exige decisão antecipada. Quando o operador percebe que o consumo vai ser alto na ponta, muitas vezes já é tarde para agir.

A pergunta desse projeto foi: **dá para prever o consumo de energia dos próximos 15 minutos com antecedência suficiente para tomar uma decisão antes que o horário caro comece?**

## O que foi feito

Usei dados reais da DAEWOO Steel Co., uma usina siderúrgica da Coreia do Sul. São 35.040 registros de consumo elétrico ao longo de 2018, medidos a cada 15 minutos.

Construí um modelo de regressão — diferente do projeto de manutenção preditiva, aqui o modelo não responde "vai falhar ou não". Ele responde um número: "o consumo nos próximos 15 minutos vai ser X kWh".

Para isso, ensinei o modelo a enxergar padrões no tempo: o quanto foi consumido nos últimos 15 minutos, na última hora, no mesmo horário do dia anterior, e assim por diante.

## Um problema que encontrei no caminho

Durante a análise, percebi que algumas variáveis do dataset eram medidas ao mesmo tempo que o consumo — ou seja, o modelo estaria "olhando a resposta antes de responder". Isso inflou os resultados iniciais para um R² de 0.994, que parecia bom demais.

Depois de remover essas variáveis, o modelo caiu para R² de 0.885 — um resultado menor, mas honesto. Porém, em produção real, só esse segundo modelo funcionaria.

## Resultados

- Erro médio de **6,69 kWh** por intervalo de 15 minutos
- R² de **0,885** — modelo explica 88,5% da variação no consumo
- O modelo acerta bem os picos de produção, que são exatamente os momentos críticos para a gestão de energia

## Quanto isso vale em dinheiro

Usando as tarifas industriais públicas da ANEEL e assumindo que é possível deslocar 15% da carga nos intervalos bem previstos — uma premissa conservadora:

| Situação | Custo no horário de ponta |
|---|---|
| Sem modelo | R$ 39.040 |
| Com modelo | R$ 36.420 |
| **Economia estimada no período** | **R$ 2.620** |
| **Projeção anual** | **R$ 9.170/ano** |

Observação: esses números são baseados em dados reais e tarifas pública.

## O que o modelo aprendeu

A variável mais importante foi o consumo dos últimos 15 minutos (lag_1), com 84,8% do poder preditivo. Faz sentido: uma fábrica siderúrgica tem grande inércia operacional — os fornos não mudam de regime de um momento para outro. O passado recente é o melhor preditor do futuro próximo.

## Como rodar

```bash
# Clone o repositório
git clone https://github.com/adalciohugo/steel-energy-consumption-prediction

# Instale as dependências
pip install -r requirements.txt

# Abra o notebook
jupyter notebook previsao_consumo_energetico_industrial.ipynb
```

## Arquivos

```
├── previsao_consumo_energetico_industrial.ipynb  ← análise completa com comentários
├── Steel_industry_data.csv                       ← dataset original (UCI)
├── painel_resultados.png                         ← visualização consolidada
├── requirements.txt
└── README.md
```

## Fonte dos dados

Steel Industry Energy Consumption Dataset — UCI Machine Learning Repository  
https://archive.ics.uci.edu/dataset/851/steel+industry+energy+consumption

Empresa: DAEWOO Steel Co. Ltd — Gwangyang, Coreia do Sul (2018)

## Autor

**Adálcio Hugo Gonçalves dos Santos**  
Engenheiro de Materiais — CEFET-MG  
[LinkedIn](https://linkedin.com/in/adalcio-hugo) · [GitHub](https://github.com/adalciohugo)
