# Pandas Kaggle

## Creating, reading and writing

```python
#importar biblioteca
import pandas as pd

#criar df
dados = pd.DataFrame({'Yes': [50, 21], 'No': [131, 2]})

#criar df e mudar o indice
pd.DataFrame({'Bob': ['I liked it.', 'It was awful.'], 
              'Sue': ['Pretty good.', 'Bland.']},
             index=['Product A', 'Product B'])

#criar serie (tabela de uma unica coluna)
pd.Series([1, 2, 3, 4, 5])

#criar serie mudando o indice
pd.Series([30, 35, 40], index=['2015 Sales', '2016 Sales', '2017 Sales'], name='Product A')

#ler aquivos csv
dados = pd.read_csv('diretorio_do_arquivo.csv')

#fazer o pandas reconhecer uma coluna como indice
reviews = pd.read_csv('../input/wine-reviews/winemag-data_first150k.csv', index_col = 0)

#mostrar o tamanho do df
reviews.shape

#mostrar as 5 primeiras linhas
reviews.head()

#salvar informaçoes no formato csv
reviews.to_csv('reviews.csv')

```

## ****Indexing, Selecting & Assigning****

```python
#para selecionar apenas uma coluna do df
desc = reviews['description']

#podemos acessar tambem como um atributo
desc = reviews.desciption

#para saber qual o tipo do objeto é desc usamos
type(desc)

#se quisermos pegar um item especifico podemos fazer como nos dicionarios
reviews['country'][0]

#para selecionar a primeira linha de um df podemos usar o iloc
reviews.iloc[0]
#outra maneira é
reviws.description[0]

#para recuperar colunas podemos fazer
review.iloc[:, 0]
# o operador : significa tudo ou um intervalo
#se quisermos pegar tudo até a terceira linha fazemos
reviews.iloc[:3, 0]
#podemos tambem passar uma lista como parametro
reviews.iloc[[0, 1, 2], 0]
#podemos tambem usar numeros negativos para pegar os ultimos valores
reviews.iloc[-5:]
#nesse caso pegamos os 5 ultimos valores

#ja o operador loc é para valores de indice e nao posição
# por exemplo, para pegar a primeira entrada em reviews fazemos
reviews.loc[0, 'country']

#iloc é mais simples que loc por que iguinora os indices do df
#enquanto iloc trata o df como uma grande matriz (lista de listas) 
#na qual devemos indexar por posição
#loc por outro lado usa a informação dos indices para fazer seu trabalho
#como seu df geralmente usa indices significativos é mais facil usar loc

#exemplo de operação que é mais facil usando loc
reviews.loc[:, ['taster_name', 'taster_twitter_handle', 'points']]

#ao usar iloc o primeiro elemento é incluido e o ultimo é excluido

#para gerar um df apenas com linhas especificas posso fazer
sample_reviews = reviews.iloc[[1, 2, 3, 5, 8] :]

#para gerar um df com linhas e colunas especificas
indexes = [0, 1, 10, 100]
columns = ['country', 'province', 'region_1', 'region_2']

df = reviews.iloc[indexes][columns]

#quando queremos mudar o indice para alguma coluna ja especifica 
reviews.set_index('title')

#podemos selecionar o df baseado em perguntas logicas
#por exemplo vinhos apenas feitos na italia
reviews.country == 'Italy'
#essa operação retorna uma serie booleana com base no pais de cada vinho
#esse resultado pode ser usado no loc para selecionar dados relevantes
reviews.loc[reviews.country == 'Italy']
#essa linha retorna apenas os vinhos italianos
#podemos tambem colocar mais de uma condição
#exemplo, vinhos italianos e com nota acima da media
reviews.loc[(reviews.country == 'Italy') & (reviews.points >= 90)]
#vai retornar um df com a busca especifica

#podemos usar os operadores &(e) |(ou) ==(igual) etc
#Pandas ja vem com condicionais built-in
#os principais sao isin e isnull / notnull
#isin permite selecionar dados cujo valor "está em" uma lista de valores.
#exemplo, vinhos apenas da italia ou frança
reviews.loc[reviews.country.isin(['Italy', 'France'])

#ja os isnull e notnull selecionam valores que estao ou nao nulos
#exemplo, filtrar vinhos sem etiqueta de preço
reviews.loc[reviews.price.notnull()]

#podemos tambem atribuir valores no df
#podemos atribuir um valor constante
reviews['critic'] = 'everyone'
#agora todo o df tem 'everyone' na coluna critic

#podemos tambem usar valores iteraiveis
reviews['index_backwards'] = range(len(reviews), 0, -1)
#nessa linha colocamos indice de tras pra frente começando por 1
```

## Summary Functions and Maps

```python
#existem varias funçoes no pandas para gerar 'resumos' dos dados
#uma delas é a describe()
reviews.points.describe()
#para entradas numericas o metodo describe() retorna a media, mediana, contagem, desvio padrao
#para entradas string ele retorna a contagem, a quantidade de valores unicos, o mais repetido

#para estatisticas mais especificas geralmente o Pandas tem um metodo pra isso
#por exemplo se queremos a media de pontuação de cada vinho, podemos usar o metodo mean()
reviews.points.mean()
#temos o metodo median() para mediana
reviews.points.median()

#para ver uma lista com os valores unicos usamos unique()
reviews.taster_name.unique()

#para ver uma lista com valores unicos e sua frequencia usamos value_counts()
reviews.taster_name.value_counts()

#mapa é um termo roubado da matematica para uma função que pega um conjunto de valores e "mapeia" eles em outro conjunto de valores
#isso é util para criar novas representações para os dados existentes, ou transformar dados de um formato para outro que queremos usar depois
#mapas sao responsaveis por esse trabalho, o que os torna extremamente importante

#REVISAR FUNÇAO MAP, NAO ENTENDI DIREITO
#aqui estao dois metodos map que usaremos a frente
#map() é o primeiro e mais simples
#exemplo, queremos redefinir a  media da pontuação dos vinhos para 0
reviews_points_mean = reviews.points.mean()
reviews.points.map(lambda p : p - review_points_mean)
#o metodo map, sempre recebe uma função e retorna uma serie com o resultado da manipulação

#o metodo apply() é equivalente ao map, mas ele transforma todo o df chamando um metodo personalizado para cada linha
#exemplo de função apply()
def remean_points(row):
	row.points = row.points - review_points_mean
	return row

reviews.apply(remean_points, axis='columns')
#se passarmos a função apply com axis='index' ao inves de psasar uma funçao que transforma cada linha
#seria necessario passar uma funçao para transformar cada coluna.

#o pandas entenderá oq fazer se quisermos modificar series de mesmo tamanho.
#exemplo, se quisermos combinar o pais e a regiao no df
reviews.country + ' - ' + reviews.region_1
#a saida será Italy - Etna
#esses operadores sao mais rapidos que map() ou apply(). Todos os operadores do python funcionam dessa maneira

#se eu quiser contar quantas vezes uma palavra especifica aparece em uma coluna
#eu posso fazer o seguinte
a = len(reviews[(reviews['description'].str.contains('tropical'))]['description'])
b = len(reviews[(reviews['description'].str.contains('fruity'))]['description'])
descriptor_counts = pd.Series([a,b],index=['tropical','fruity'])
#esse codigo retorna uma serie com o numero de aparicoes de cada palavra

```

## Grouping and Sorting

```python
#mapas nos permite transformar dados em um df ou serie um valor por vez de uma coluna inteira
#No entanto, muitas vezes queremos agrupar nossos dados e depois fazer algo específico para o grupo em que os dados estão.
#para isso usamos o groupby()

#analise de grupo
#se quisermos obter o vinho mais barato de cada valor de pontos
reviews.groupby('points').price.min()

```