# Precariedade habitacional nos setores censitários da Região Metropolitana de São Paulo

Análise exploratória e agrupamento (K-Means) das condições de moradia dos domicílios nos setores censitários dos 39 municípios da Região Metropolitana de São Paulo (RMSP), a partir do Censo Demográfico 2022 (IBGE).

## Estrutura do projeto

```
.
├── data/                      # dados do IBGE (NÃO versionados — ver "Como obter os dados")
├── BD_setores_RMSP.ipynb      # 1. seleciona os setores da RMSP e consolida a base
├── AN_setores_RMSP.ipynb      # 2. preparação, análise descritiva, agrupamento e visualizações
├── .gitignore
└── README.md
```

## Como obter os dados

Os arquivos do IBGE são muito grandes para o GitHub e, por isso, **não estão neste repositório**. Eles devem ser baixados da fonte oficial e colocados na pasta `data/`.

### 1. Baixe os arquivos no site do IBGE

Acesse a aba de downloads do Censo Demográfico 2022 no site do IBGE:

**https://www.ibge.gov.br/estatisticas/sociais/saude/22827-censo-demografico-2022.html?=&t=downloads**

Na lista de pastas, abra:

**Agregados por Setores Censitários → Agregados_por_Setor_[formato]**

Baixe estes **quatro** arquivos (o de alfabetização não é usado):

| Arquivo no IBGE | Conteúdo | Versão usada |
|---|---|---|
| `Agregados_por_setores_basico_BR_20260520.zip` | Tabela Básico (identificação dos setores, municípios e totais) | 20/05/2026 |
| `Agregados_por_setores_caracteristicas_domicilio1_BR_20261021.zip` | Características dos Domicílios 1 | 21/10/2026 |
| `Agregados_por_setores_caracteristicas_domicilio2_BR_20250417.zip` | Características dos Domicílios 2 | 17/04/2025 |
| `Agregados_por_setores_caracteristicas_domicilio3_BR_20250417.zip` | Características dos Domicílios 3 | 17/04/2025 |

> A data no nome do arquivo indica a versão publicada pelo IBGE. Se houver uma versão mais recente, os resultados podem diferir ligeiramente dos apresentados.

### 2. Descompacte e renomeie

Descompacte os quatro arquivos e salve as tabelas na pasta `data/` com estes nomes:

```
data/
├── basico.csv
├── domicilio1.csv
├── domicilio2.csv
└── domicilio3.csv
```

Os notebooks esperam arquivos `.csv` com separador `;` e codificação `latin1` (padrão do IBGE). Se os arquivos baixados estiverem em outro formato ou separador, ajuste os parâmetros `SEP` e `ENCODING` no início do notebook `BD_setores_RMSP.ipynb`.

### 3. Malha de setores censitários (mapas)

A malha de setores censitários de 2022 do estado de São Paulo é baixada **automaticamente** pelo notebook de análise, na primeira execução, a partir do servidor do IBGE. Uma cópia apenas com os setores da RMSP é salva em `data/setores_RMSP_2022.gpkg` para as execuções seguintes.

## Como rodar

1. Instale as bibliotecas (de preferência num ambiente virtual):
   ```
   pip install pandas numpy scikit-learn scipy matplotlib seaborn plotly geopandas requests
   ```
2. Coloque os arquivos do IBGE em `data/`, como descrito acima.
3. Rode `BD_setores_RMSP.ipynb`. Ele gera `setores_rmsp.csv` (uma linha por setor da RMSP).
4. Rode `AN_setores_RMSP.ipynb` do início ao fim.

## Principais decisões de preparação dos dados

- **Setores sem dados de domicílios** (sem domicílios ocupados ou com todos os dados omitidos por sigilo): excluídos.
- **Valores omitidos por sigilo:** o IBGE omite as contagens de 1 e 2 domicílios; os valores ausentes foram substituídos por 1,5.
- **Taxas:** variáveis dos domicílios permanentes divididas pelo total de domicílios permanentes ocupados (V00001); tipos de domicílio improvisado divididos pelo total de domicílios ocupados (v0007).
- **Valores extremos:** limitados ao percentil 99 antes da normalização Min-Max.
- **Agrupamento:** K-Means com semente fixa (`random_state=42`), o que garante os mesmos resultados a cada execução.

## Fonte

IBGE – Instituto Brasileiro de Geografia e Estatística. **Censo Demográfico 2022**: Agregados por Setores Censitários. Rio de Janeiro: IBGE.
