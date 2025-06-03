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

## ✅ Zero ou negativo onde não deveria (ex: tempo, distância)

## ✅ Duplicatas sem ser idênticas (ex: registros com nomes quase iguais, mas não exatos)
