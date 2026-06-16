"""# 🏥 Análise Preditiva e Prescritiva da Expectativa de Vida Global

Este projeto prático de Ciência de Dados tem como objetivo limpar, explorar, modelar e simular cenários de políticas públicas com base em um dataset de indicadores socioeconômicos e de saúde global. O fluxo de trabalho foi dividido em 8 etapas rigorosas, cobrindo desde a inspeção estrutural até a modelagem preditiva avançada e prescrição de políticas públicas.

---

## 📊 Sobre o Dataset

| Atributo | Valor |
| :--- | :--- |
| **Arquivo de entrada** | `expectativa_vida_2016_2025.csv` |
| **Linhas (bruto)** | 1.945 |
| **Colunas (bruto)** | 22 |
| **Período** | 2016 – 2025 |
| **Países distintos** | 233 |
| **Variável-alvo** | `life_expectancy` |

---

## 🔍 1. Análise Exploratória (EDA) e Principais Insights

Durante a inspeção inicial e exploração visual dos dados, os seguintes padrões foram identificados:

*   **Distribuição da Variável-Alvo:** A expectativa de vida global possui distribuição **bimodal** e assimétrica à esquerda, refletindo a desigualdade mundial. A média global é de **72,53 anos**, com picos em ~60–65 anos (países em desenvolvimento) e ~75–80 anos (países desenvolvidos).
*   **Desigualdade Regional:** O teste de Mann-Whitney (p-valor = 2,86 × 10⁻¹¹⁴) confirmou uma diferença altamente significativa entre os grupos. A mediana em países desenvolvidos é de **81,88 anos**, contra **72,10 anos** nos em desenvolvimento (quase 10 anos de gap).
*   **Dados Ausentes:** Nenhuma coluna ultrapassou 10% de dados faltantes (maiores taxas em `gdp` e `population`).
*   **Assimetria (Skewness):** Variáveis com alta assimetria foram identificadas e tratadas com transformações logarítmicas. Exemplo: a assimetria do `gdp` caiu de 26,37 para -0,65 após a aplicação do log.

---

## ⚙️ 2. Engenharia de Dados e Feature Engineering

### Data Munging (Limpeza e Imputação)
Implementação de uma esteira de imputação robusta e automatizada em **3 camadas**: 
1. Mediana do próprio país;
2. Fallback para a mediana do status socioeconômico;
3. Fallback para a mediana global.
**Resultado:** Entrega do dataset `df_clean.csv` com **0 valores ausentes** e padronização binária da coluna de status (*Developed* / *Developing*).

### Criação de Variáveis (Feature Engineering)
As variáveis foram consolidadas para reduzir a dimensionalidade e aumentar o poder preditivo:

| Feature Nova | Origem | Motivação |
| :--- | :--- | :--- |
| `log_gdp` / `log_population` | `np.log1p()` | Correção de assimetria extrema |
| `vaccine_coverage_avg` | Média de HepB, Polio, DTP | Índice sintético de imunização |
| `disease_burden` | `hiv + measles` | Pressão agregada de doenças infecciosas |
| `thinness_avg` | Média de magreza (1-19 e 5-9 anos) | Evita multicolinearidade |
| `status_enc` | Desenvolvido (1) / Em Desenvolvimento (0)| Encoding numérico |
| `year_norm` | `(year - 2016) / 9` | Tendência temporal normalizada [0, 1] |
| `mortality_le_ratio` | `adult_mortality / life_expectancy`| Mortalidade relativa à longevidade |

### Saneamento Estatístico
Mitigação de multicolinearidade perfeita (`VIF = inf`) garantindo que todos os preditores finais enviados ao modelo possuíssem um Fator de Inflação de Variância seguro (`VIF < 10`).

---

## 🤖 3. Modelagem Preditiva

Foram testados e comparados algoritmos lineares, de regularização e baseados em árvores (Ensemble):
*   **Modelos Avaliados:** Regressão Linear, Ridge, Lasso, Random Forest, Gradient Boosting e XGBoost.
*   **Resultado:** Os modelos baseados em árvores de decisão superaram amplamente os benchmarks da regressão linear simples, apresentando alto R² na validação cruzada e minimizando o Erro Absoluto Médio (MAE).

---

## 🎯 4. Simulação de Políticas Públicas (Desafio Extra)

Utilizando a técnica de reamostragem estatística (*Bootstrapping* com 500 interações) sobre um país em desenvolvimento da base de teste, simulamos dois cenários para auxiliar a tomada de decisão de um gestor público:

| Cenário Simulados | Expectativa Prevista | IC Inferior (95%) | IC Superior (95%) | Impacto Real no País |
| :--- | :---: | :---: | :---: | :---: |
| **Cenário Atual (Baseline)** | 71.19 anos | 69.41 anos | 73.67 anos | Ref. Atual |
| **Cenário A (Gastos em Saúde +20%)** | 71.59 anos | 69.94 anos | 74.18 anos | +0.40 anos (~4.8 meses) |
| **Cenário B (Controle do HIV para 0.1)**| **71.84 anos** | **70.00 anos** | **74.28 anos** | **+0.65 anos (~7.8 meses)** |

> ### 📢 Recomendação Baseada em Dados
> Os dados provam que o **Cenário B (Ação Cirúrgica contra o HIV/AIDS)** gera um ganho na longevidade da população **62,5% maior** do que uma injeção genérica de capital no orçamento da saúde (Cenário A). O HIV atua como um teto epidemiológico severo na base analisada; portanto, focar investimentos em programas verticais de distribuição de antirretrovirais e profilaxia traz, de longe, o maior retorno social e estatístico por cada centavo investido.

---

## 🛠️ Tecnologias e Bibliotecas

*   **Linguagem:** Python
*   **Manipulação e Matemática:** `pandas`, `numpy`, `scipy`
*   **Visualização:** `matplotlib`, `seaborn`
*   **Estatística Avançada:** `statsmodels` (OLS, VIF)
*   **Machine Learning:** `scikit-learn` (RF, GB, Scalers, Cross-Validation), `xgboost`

---

## 📂 Estrutura do Repositório

```text
.
├── notebooks/
│   └── exercicio_aula18.ipynb          # Notebook principal completo
├── data/
│   ├── expectativa_vida_2016_2025.csv  # Dataset original de entrada
│   └── df_clean.csv                    # Dataset limpo (Gerado na Etapa 2)
├── documentacao.html                   # Documentação detalhada exportada
├── requirements.txt                    # Dependências do projeto
└── README.md                           # Visão geral do projeto