# Projeto de Predição de Consumo de Veículos

Projeto de exploração e predição do consumo combinado de veículos com a base `vehicles.csv`. O notebook investiga os dados e treina modelos para prever `comb08`; a modelagem usa uma cópia de trabalho e um pipeline de pré-processamento, sem alterar a base original.

## Estrutura

```text
data/
  raw/vehicles.csv              # base usada pelo notebook
notebooks/
  01_analise_exploratoria.ipynb
outputs/figures/
requirements.txt
```

O arquivo `data/raw/vehicles.csv` é a base carregada pelo notebook. A base foi obtida no [FuelEconomy.gov](https://www.fueleconomy.gov/feg/download.shtml), que disponibiliza os dados em CSV. O notebook procura o arquivo nesse caminho relativo; mantenha-o no local indicado ao clonar o projeto.

## Ambiente e dependências

Com Python 3 instalado, no PowerShell e na raiz do projeto, crie o ambiente virtual e instale as dependências:

```powershell
py -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
```

No VS Code, abra `notebooks/01_analise_exploratoria.ipynb` e selecione o kernel do ambiente `.venv`.

## Executar

No VS Code, execute as células em ordem, começando pelo carregamento. Para abrir a interface do Jupyter pelo PowerShell:

```powershell
.\.venv\Scripts\python.exe -m jupyter notebook notebooks/01_analise_exploratoria.ipynb
```

O notebook procura `data/raw/vehicles.csv` no diretório atual e nos diretórios pais, para funcionar tanto com a raiz do projeto quanto com `notebooks/` como diretório atual.

## Leitura dos resultados

- Linhas e colunas mostram o tamanho da base e ajudam a dimensionar análises posteriores.
- Nomes de colunas ajudam a identificar atributos e localizar `comb08`, usado como variável-alvo na etapa de modelagem.
- Tipos e `DataFrame.info()` ajudam a distinguir números/categorias e a localizar possíveis ausências ou campos de tipo misto.
- `describe()` resume somente variáveis numéricas; observe escalas, dispersão, quartis e extremos.

## Análise de valores ausentes

As células finais do notebook apresentam, por coluna, o tipo, o número de valores presentes e ausentes, o percentual de ausência e uma faixa orientativa. Também resumem quantos campos faltam por registro, mostram pares de campos ausentes em conjunto e comparam a ausência de algumas variáveis por combustível, classe e faixa de ano. Grupos com menos de 100 registros são omitidos dessas comparações para evitar conclusões baseadas em grupos muito pequenos.

As faixas (0%, até 5%, >5–20%, >20–50%, >50–80% e >80%) são uma triagem, não regras automáticas. Com poucas ausências, pode-se comparar casos completos com imputação simples ou condicional; com proporções maiores, investigue aplicabilidade, padrões por subgrupo e indicadores de ausência antes de imputar ou excluir a variável. Campos quase totalmente ausentes não devem receber preenchimento global sem justificativa. A escolha também depende do tipo de dado, do objetivo e da validação do modelo.

As células exploratórias são apenas diagnóstico: não preenchem valores, não removem registros ou colunas e não salvam alterações na base. Na modelagem, imputação e codificação são aplicadas dentro do pipeline, sem alterar a base original. Valores como `-1` e `0` são mantidos como observados até que a documentação confirme se são códigos especiais. Ausência pode significar “não aplicável”; comparações por grupo ajudam a investigar essa hipótese, mas não provam sozinhas o mecanismo de ausência.

## Avaliação de registros duplicados

A seção final do notebook conta linhas exatamente repetidas em todas as colunas, examina valores ausentes e unicidade de `id`, e verifica linhas que só se repetem quando `id` é ignorado. Ela também explora uma chave descritiva (`year`, `make`, `model`, `trany`, `drive`) e apresenta variações de combustível, cilindrada, classe e identificador de motor nos grupos encontrados.

Uma correspondência por chave parcial não é, por si só, uma duplicata: um mesmo ano, marca, modelo, transmissão e tração podem ter diferentes motorizações ou versões. O resumo separa linhas pertencentes a grupos repetidos das ocorrências excedentes à primeira. Nenhum registro é removido nem alterado; qualquer deduplicação futura exige confirmar a regra de identidade adequada ao objetivo e à documentação da fonte.

## Categorias de combustível

A seção de combustíveis no notebook lista as frequências de `fuelType`, `fuelType1` e `fuelType2`, incluindo valores ausentes, e mostra um gráfico de barras horizontais com todas as categorias observadas de `fuelType`. `fuelType` é o rótulo geral, `fuelType1` identifica o combustível principal em categorias mais padronizadas e `fuelType2` registra um combustível secundário quando aplicável; não são campos intercambiáveis.

Na base analisada, `fuelType` tem 16 categorias sem ausentes; `fuelType1` tem 7 categorias e 2 ausentes; `fuelType2` tem 4 categorias observadas e 48.394 ausentes. Nenhum combustível é filtrado, removido ou reclassificado nesta etapa. Decida qualquer restrição depois de considerar o objetivo e o significado das categorias combinadas.

Valores como `-1` podem ser códigos especiais, e valores extremos podem ter explicações próprias. Confirme seus significados antes de limpar ou transformar dados. Variáveis relacionadas ao consumo também devem ser avaliadas quanto a possível vazamento ao modelar `comb08`.

## Importância das features do Random Forest

Após o ajuste do Random Forest, o notebook associa `feature_importances_` aos nomes produzidos pelo pré-processador, incluindo cada coluna criada pelo `OneHotEncoder`. A tabela lista todas as features em ordem decrescente, e o gráfico horizontal destaca as 15 primeiras. A importância é baseada na redução média de impureza nas árvores: descreve como o modelo usou as features, mas não demonstra causalidade. Categorias de uma mesma variável podem ter a importância dividida entre várias colunas one-hot.