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

## ✅ Erros de digitação numérica (ex: 1,0000 vs. 10000)

## ✅ Zero ou negativo onde não deveria (ex: tempo, distância)

## ✅ Duplicatas sem ser idênticas (ex: registros com nomes quase iguais, mas não exatos)
