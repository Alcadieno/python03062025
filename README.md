# Análise de Dados: COVID-19 no Brasil - Despesas e Transparência Pública

## 📋 Visão Geral do Projeto

Este projeto realiza uma análise abrangente dos dados de despesas relacionadas ao combate à COVID-19 no Brasil, utilizando dados do Portal da Transparência do Governo Federal. O objetivo é fornecer insights sobre como os recursos públicos foram distribuídos durante a pandemia, identificando padrões de gastos, principais beneficiários e conexões entre parlamentares.

## 🎯 Objetivos

- **Transparência**: Analisar como os recursos públicos foram utilizados no combate à COVID-19
- **Visualização**: Criar representações gráficas claras dos dados de despesas
- **Conexões**: Identificar relacionamentos entre parlamentares através de frentes parlamentares
- **Temporal**: Acompanhar a evolução dos gastos ao longo do tempo
- **Ranking**: Identificar os principais beneficiários dos recursos

## 📊 Estrutura do Projeto

```
CovidNoBrasil/
├── notebook/
│   ├── diagramaDeFluxo.ipynb      # Análise de conexões entre deputados
│   ├── graficoEmBarras.ipynb      # Ranking de parlamentares por valores
│   └── serieTemporal.ipynb        # Evolução temporal dos gastos
├── dados/
│   ├── despesas_coronavirus.csv   # Dataset principal
│   └── orgaos.csv                 # Dados dos órgãos executores
└── README.md
```

## 🔍 Análises Implementadas

### 1. Diagrama de Fluxo (diagramaDeFluxo.ipynb)

**Objetivo**: Visualizar conexões entre deputados através de frentes parlamentares comuns.

**Tecnologias Utilizadas**:
- `pandas` - Manipulação de dados
- `holoviews` - Visualização interativa
- `itertools.combinations` - Geração de pares
- `collections.Counter` - Contagem de ocorrências

**Implementação**:
```python
# Deputados de interesse selecionados
nomes_alvo = {
    "Daniel Silveira", "Flordelis", "Eduardo Bolsonaro",
    "André Janones", "Arthur Lira", "Tabata Amaral"
}

# Processamento dos dados
df_filtrado = df[df["deputado_.nome"].isin(nomes_alvo)]
frentes = df_filtrado.groupby("titulo")["deputado_.nome"].apply(list)

# Geração de pares e contagem
pares = []
for deputados in frentes:
    if len(deputados) > 1:
        pares.extend(combinations(sorted(deputados), 2))
```

**Características**:
- **Chord Diagram**: Visualização circular mostrando conexões
- **Cores diferenciadas**: Paleta pastel para cada deputado
- **Interatividade**: Hover para detalhes das conexões
- **Peso das conexões**: Espessura proporcional ao número de frentes em comum

### 2. Gráfico em Barras (graficoEmBarras.ipynb)

**Objetivo**: Ranking dos parlamentares que mais receberam recursos via emendas para COVID-19.

**Tecnologias Utilizadas**:
- `pandas` - Processamento de dados
- `matplotlib.pyplot` - Visualização
- `unidecode` - Normalização de texto

**Implementação**:
```python
# Limpeza e normalização dos dados
df["Valor Pago"] = df["Valor Pago"].str.replace(".", "", regex=False)
df["Valor Pago"] = df["Valor Pago"].str.replace(",", ".", regex=False)
df["Valor Pago"] = pd.to_numeric(df["Valor Pago"], errors="coerce")

# Filtros específicos para COVID-19
filtro1 = "21C0 - ENFRENTAMENTO DA EMERGENCIA DE SAUDE PUBLICA..."
filtro2 = "2F01 - REFORCO DE RECURSOS PARA EMERGENCIA..."

# Sistema de cores por faixas de valores
def get_color(valor):
    if valor <= 500_000: return '#c6dbef'
    elif valor <= 1_000_000: return '#9ecae1'
    # ... outras faixas
```

**Características**:
- **Top 10**: Mostra os 10 parlamentares com maiores valores
- **Cores graduadas**: Sistema de cores baseado em faixas de valores
- **Formatação brasileira**: Valores em reais com formatação local
- **Barras horizontais**: Melhor legibilidade para nomes longos

### 3. Série Temporal (serieTemporal.ipynb)

**Objetivo**: Heatmap mostrando evolução mensal dos gastos por órgão executor em 2021.

**Tecnologias Utilizadas**:
- `pandas` - Manipulação temporal
- `seaborn` - Heatmap avançado
- `matplotlib` - Customização visual
- `calendar` - Formatação de meses

**Implementação**:
```python
# Conversão e filtro temporal
df["Mês Ano"] = pd.to_datetime(df["Mês Ano"], format="%m/%Y")
df = df[df["Mês Ano"].dt.year == 2021]

# Criação de nomes de meses
df["Mês Nome"] = df["Mês Ano"].dt.month.apply(lambda x: calendar.month_name[x])

# Agrupamento e pivot
df_grouped = df.groupby(["Mês Nome", "Órgão/Entidade Vinculada Executora"])["Valor Pago"].sum()
df_pivot = df_grouped.pivot(index="Órgão/Entidade", columns="Mês Nome", values="Valor Pago")

# Normalização para melhor visualização
norma = PowerNorm(gamma=0.3, vmin=0, vmax=df_pivot.max().max())
```

**Características**:
- **Heatmap mensal**: Visualização de 12 meses de 2021
- **Normalização PowerNorm**: Realça valores menores para melhor percepção
- **Formatação monetária**: Valores em reais brasileiros
- **Ordenação temporal**: Meses em ordem cronológica correta

## 📈 Principais Insights

### Conexões Parlamentares
- Identificação de grupos de deputados que atuam em frentes comuns
- Visualização de alianças e colaborações políticas
- Análise de polarização através das conexões

### Distribuição de Recursos
- Ranking transparente dos maiores beneficiários
- Identificação de concentração de recursos
- Análise de equidade na distribuição

### Padrões Temporais
- Picos de gastos em momentos críticos da pandemia
- Sazonalidade dos desembolsos
- Eficiência temporal na execução orçamentária

## 🛠️ Tecnologias e Bibliotecas

### Core Data Science
- **pandas**: Manipulação e análise de dados
- **numpy**: Operações numéricas (implícito)

### Visualização
- **matplotlib**: Gráficos base e customização
- **seaborn**: Visualizações estatísticas avançadas
- **holoviews**: Visualizações interativas e dinâmicas

### Processamento de Texto
- **unidecode**: Normalização de caracteres especiais
- **calendar**: Manipulação de datas e meses
- **locale**: Formatação regional brasileira

### Utilitários
- **itertools**: Operações combinatórias
- **collections**: Estruturas de dados especializadas

## 🚀 Como Executar

### Pré-requisitos

```bash
pip install pandas matplotlib seaborn holoviews unidecode bokeh
```

### Configuração do Ambiente

1. Clone o repositório:
```bash
git clone https://github.com/Alcadieno/python03062025.git
cd python03062025/CovidNoBrasil
```

2. Configure o locale brasileiro (opcional, para formatação de datas):
```bash
sudo locale-gen pt_BR.UTF-8
```

### Execução dos Notebooks

1. **Diagrama de Fluxo**:
```bash
jupyter notebook notebook/diagramaDeFluxo.ipynb
```

2. **Gráfico em Barras**:
```bash
jupyter notebook notebook/graficoEmBarras.ipynb
```

3. **Série Temporal**:
```bash
jupyter notebook notebook/serieTemporal.ipynb
```

## 📋 Estrutura dos Dados

### Dataset Principal (despesas_coronavirus.csv)
- **Separador**: `;` (ponto e vírgula)
- **Encoding**: UTF-8
- **Origem**: Portal da Transparência do Governo Federal
- **Colunas principais**:
  - `deputado_.nome`: Nome do deputado
  - `Autor Emenda`: Autor da emenda parlamentar
  - `Valor Pago`: Valor em formato brasileiro (R$ X.XXX,XX)
  - `Ação Orçamentária`: Descrição da ação governamental
  - `Órgão/Entidade Vinculada Executora`: Órgão executor
  - `Mês Ano`: Data no formato MM/YYYY
  - `titulo`: Título da frente parlamentar

### Dataset Secundário (orgaos.csv)
- Informações complementares sobre órgãos executores
- Dados de vinculação e hierarquia organizacional

## 🔧 Tratamento de Dados

### Limpeza Padrão Aplicada
1. **Remoção de valores nulos** em campos críticos
2. **Normalização de texto** com unidecode para caracteres especiais
3. **Conversão monetária** de formato brasileiro (X.XXX,XX) para float
4. **Filtros específicos** para ações relacionadas à COVID-19:
   - `21C0 - ENFRENTAMENTO DA EMERGENCIA DE SAUDE PUBLICA...`
   - `2F01 - REFORCO DE RECURSOS PARA EMERGENCIA...`
5. **Padronização de nomes** de parlamentares (remoção de prefixos)

### Validações Implementadas
- Exclusão de bancadas (foco em deputados individuais)
- Verificação de códigos de autor válidos (≠ "0000")
- Tratamento de erros de conversão numérica com `errors="coerce"`
- Filtros de consistência para dados não nulos

## 📊 Outputs Esperados

### Diagrama de Fluxo
- **Formato**: Chord diagram interativo (Bokeh)
- **Dimensões**: 1000x1000 pixels
- **Características**:
  - Conexões proporcionais ao número de frentes comuns
  - Cores diferenciadas por deputado (paleta pastel)
  - Labels com nomes dos deputados
  - Hover tooltips para detalhes

### Gráfico em Barras
- **Formato**: Barras horizontais
- **Dimensões**: Dinâmicas (25 x altura baseada no número de deputados)
- **Características**:
  - Top 10 parlamentares com maiores valores
  - Sistema de cores graduado por faixas de valores
  - Valores formatados em reais brasileiros dentro das barras
  - Grid horizontal para melhor leitura

### Série Temporal
- **Formato**: Heatmap
- **Dimensões**: 30 x altura dinâmica
- **Características**:
  - Matriz 12 meses x N órgãos
  - Gradiente de cores OrRd com normalização PowerNorm
  - Valores anotados em formato monetário brasileiro
  - Total geral calculado e exibido

## 🎨 Paletas de Cores Utilizadas

### Diagrama de Fluxo
```python
cores_pastel = [
    '#66c2a5',  # verde água
    '#fc8d62',  # laranja claro
    '#8da0cb',  # azul lavanda
    '#e78ac3',  # rosa claro
    '#a6d854',  # verde limão
    '#ffd92f',  # amarelo suave
    '#e5c494'   # bege
]
```

### Gráfico em Barras
Sistema graduado baseado em faixas de valores:
- Até R$ 500.000: `#c6dbef`
- Até R$ 1.000.000: `#9ecae1`
- Até R$ 2.000.000: `#6baed6`
- Até R$ 4.000.000: `#4292c6`
- Até R$ 6.000.000: `#2171b5`
- Até R$ 8.000.000: `#08519c`
- Até R$ 10.000.000: `#08306b`
- Acima: `#041f3d`

### Série Temporal
- **Colormap**: `OrRd` (Orange-Red)
- **Normalização**: PowerNorm com gamma=0.3

## 🔍 Deputados Analisados

O projeto foca em 6 deputados específicos para análise de conexões:
- **Daniel Silveira**
- **Flordelis**
- **Eduardo Bolsonaro**
- **André Janones**
- **Arthur Lira**
- **Tabata Amaral**

Esta seleção permite análise de diferentes espectros políticos e suas interações.

## 📈 Métricas e KPIs

### Métricas Calculadas
- **Total de recursos por parlamentar**
- **Número de frentes parlamentares em comum**
- **Distribuição temporal de gastos**
- **Concentração de recursos por órgão**
- **Evolução mensal dos desembolsos**

### Indicadores de Performance
- **Eficiência temporal**: Velocidade de execução orçamentária
- **Concentração**: Índice de distribuição de recursos
- **Conectividade**: Grau de conexão entre parlamentares

## 🤝 Contribuições

Este projeto está aberto para contribuições. Áreas de interesse:

### Análises Adicionais
- Análise de correlação entre gastos e indicadores de saúde
- Comparação regional de distribuição de recursos
- Análise de efetividade dos gastos

### Melhorias Técnicas
- Otimização de performance para datasets maiores
- Implementação de testes automatizados
- Dockerização do ambiente

### Visualizações
- Dashboard interativo com Plotly Dash
- Mapas geográficos de distribuição
- Análises de rede mais complexas

## 📄 Fonte dos Dados

Os dados utilizados são provenientes do **Portal da Transparência do Governo Federal** (https://portaldatransparencia.gov.br/), garantindo a veracidade e oficialidade das informações analisadas.

### Licença dos Dados
Os dados são de domínio público, conforme Lei de Acesso à Informação (Lei nº 12.527/2011).

## 📞 Contato

Para dúvidas, sugestões ou colaborações:
- **Repositório**: [github.com/Alcadieno/python03062025](https://github.com/Alcadieno/python03062025)
- **Issues**: Use a seção de Issues do GitHub para reportar problemas

## 📚 Referências

- Portal da Transparência: https://portaldatransparencia.gov
