# Inconsistências de valores em conjunto de dados.
## ✅ Outliers extremos
* Detectar via IQR (Intervalo Interquartil) – Método comum.
  * ```Q1 = df['coluna'].quantile(0.25)```
  * ```Q3 = df['coluna'].quantile(0.75)```
  * ```IQR = Q3 - Q1```
  * ```limite_inferior = Q1 - 1.5 * IQR```
  * ```limite_superior = Q3 + 1.5 * IQR```
  * ```outliers = df[(df['coluna'] < limite_inferior) | (df['coluna'] > limite_superior)]```
* Detectar via desvio padrão (menos robusto).
  * ```media = df['coluna'].mean()```
  * ```desvio = df['coluna'].std()```
  * ```limite_inferior = media - 3 * desvio```
  * ```limite_superior = media + 3 * desvio```
  * ```outliers = df[(df['coluna'] < limite_inferior) | (df['coluna'] > limite_superior)]```
* Remover.
  * ```df_sem_outliers = df[(df['coluna'] >= limite_inferior) & (df['coluna'] <= limite_superior)]```
* Substituir por mediana ou média.
  * ```mediana = df['coluna'].median() df['coluna_corrigida'] = df['coluna'].apply(lambda x: mediana if x < limite_inferior or x > limite_superior else x```
* Limitar (winsorizar).
  * ```df['coluna_corrigida'] = df['coluna'].clip(lower=limite_inferior, upper=limite_superior)```
* Visualizar com boxplot.
  * ```import seaborn as sns```
  * ```import matplotlib.pyplot as plt```
  * ```sns.boxplot(x=df['coluna'])```
  * ```plt.show()```
## ✅ Valores impossíveis (ex: idade = 150, salário = -2000)
* Definir os limites plausíveis(idade).
  * ```limite_inf = 0```
  * ```limite_sup = 120```
* Detectar valores fora do intervalo.
  * ```valores_invalidos = df[(df['idade'] < limite_inf) | (df['idade'] > limite_sup)]```
* Substituir por NaN.
  * ```import numpy as np```
  * ```df['idade'] = df['idade'].apply(lambda x: x if limite_inf <= x <= limite_sup else np.nan)```
* Substituir pela mediana ou média.
  * ```mediana = df['idade'].median()```
  * ```df['idade'] = df['idade'].apply(lambda x: x if limite_inf <= x <= limite_sup else mediana)```
* Remover as linhas com valores inválidos.
  * ```df = df[(df['idade'] >= limite_inf) & (df['idade'] <= limite_sup)]```
* Exemplo geral.
  * ```def corrigir_valores_invalidos(df):```
  * ```regras = {```
  * ```'idade': (0, 120),```
  * ```'salario': (0, 1000000),```
  * ```'nota': (0, 100),```
  * ```'peso': (30, 300)```
  * ```}```
  * ```for coluna, (min_val, max_val) in regras.items():```
  * ```if coluna in df.columns:```
  * ```df[coluna] = df[coluna].apply(lambda x: x if min_val <= x <= max_val else np.nan)```
  * ```return df```
* Verificar valores extremos antes.
  * ```df['salario'].describe()```
  * ```df['idade'].value_counts(dropna=False).sort_index()```
## ✅ Erros de digitação numérica (ex: 1,0000 vs. 10000)
* Verifique o separador decimal do seu dataset.
  * ```df.head()```
* Substituir vírgulas por pontos (para conversão correta).
  * ```df['coluna'] = df['coluna'].str.replace(',', '.')```
  * ```df['coluna'] = pd.to_numeric(df['coluna'], errors='coerce')```
* Corrigir erros de escala. Às vezes os dados têm números 100x ou 1000x maiores do que deveriam. Se encontrar 10000, pode ser erro de digitação.
  * ```df['coluna'].describe()```
* Corrigir com base em faixa esperada.
  * ```df['coluna_corrigida'] = df['coluna'].apply(lambda x: x / 1000 if x > 1000 else x)```. Ou aplicar regras específicas baseadas em contexto.
* Exemplo completo.
  * ```import pandas as pd```
  * ```df = pd.DataFrame({```
  * ```'preco': ['1,50', '1500', '2,75', '20000']```
  * ```})```
  * ```# Corrigir separador decimal```
  * ```df['preco'] = df['preco'].str.replace(',', '.')```
  * ```df['preco'] = pd.to_numeric(df['preco'], errors='coerce')```
  * ```# Corrigir valores fora da faixa (ex: dividir os muito grandes)```
  * ```df['preco_corrigido'] = df['preco'].apply(lambda x: x / 1000 if x > 1000 else x)```
  * ```print(df)```
## ✅ Zero ou negativo onde não deveria (ex: tempo, distância)
* Como detectar no Pandas.
  * ```df[df['coluna'] <= 0]```
* Substituir por NaN (para tratar depois).
  * ```import numpy as np  df['coluna'] = df['coluna'].apply(lambda x: x if x > 0 else np.nan)```
* Substituir por média ou mediana.
  * ```mediana = df['coluna'][df['coluna'] > 0].median()```
  * ```df['coluna'] = df['coluna'].apply(lambda x: mediana if x <= 0 else x)```
* Remover linhas com valores inválidos.
  * ```df = df[df['coluna'] > 0]```
* Substituir por valor padrão (se souber o correto).
  * ```df['tempo'] = df['tempo'].replace(0, 10)```
* Aplicação geral para várias colunas.
  * ```def corrigir_valores_negativos_ou_zero(df, colunas):```
  * ```for coluna in colunas:```
  * ```df[coluna] = df[coluna].apply(lambda x: np.nan if x <= 0 else x)```
  * ```return df```
  * ```df = corrigir_valores_negativos_ou_zero(df, ['tempo', 'distancia', 'peso'])```
* Dica para investigar.
  * ```df['coluna'].value_counts().sort_index()```
  * ```df['coluna'].describe()```
## ✅ Duplicatas sem ser idênticas (ex: registros com nomes quase iguais, mas não exatos)
* Normalizar os textos (mínimo necessário).
  * ```df['nome_limpo'] = df['nome'].str.lower().str.strip().str.normalize('NFKD').str.encode('ascii', errors='ignore').str.decode('utf-8')```
* Detectar duplicatas com base no nome normalizado.
  * ```duplicados = df[df.duplicated('nome_limpo', keep=False)]```
* Verificar similaridade com fuzzy matching (opcional e poderoso).
  * ```from fuzzywuzzy import fuzz```
  * ```from fuzzywuzzy import process```
  * ```# Exemplo: comparar todos contra todos```
  * ```for i, nome1 in enumerate(df['nome_limpo']):```
  * ```for j, nome2 in enumerate(df['nome_limpo']):```
  * ```if i != j and fuzz.ratio(nome1, nome2) > 90:```
  * ```print(f'Possível duplicata: "{df["nome"].iloc[i]}" ~ "{df["nome"].iloc[j]}"')```
* Exemplo completo resumido.
  * ```import pandas as pd```
  * ```import unicodedata```
  * ```# Função para normalizar texto```
  * ```def normalizar_texto(texto):```
  * ```if not isinstance(texto, str):```
  * ```return texto```
  * ```texto = texto.lower().strip()```
  * ```texto = unicodedata.normalize('NFKD', texto).encode('ascii', errors='ignore').decode('utf-8')```
  * ```return texto```
  * ```# Aplicar```
  * ```df['nome_limpo'] = df['nome'].apply(normalizar_texto)```
  * ```# Ver duplicatas```
  * ```duplicados = df[df.duplicated('nome_limpo', keep=False)]```
