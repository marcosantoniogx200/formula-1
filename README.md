# Análise de Dados - Fórmula 1

![f1-logo](img/logo_f1.jpg "Logo da Fórmula 1")

## 1. Introdução

A Fórmula 1 é um esporte automobilístico que desperta a paixão de milhões de fãs em todo o mundo. Desde sua criação em 1950, a Fórmula 1 tem sido palco de incríveis disputas nas pistas ao redor do mundo, e a temporada de corridas anual é uma das mais aguardadas no calendário esportivo. Neste estudo, analisaremos os dados das temporadas de 2013 a 2022 para descobrir algumas informações interessantes sobre os pilotos, equipes e corridas.

### 1.1. Objetivo

O objetivo neste estudo, é realizar uma análise dos dados com os resultados das corridas das últimas 10 temporadas da Fórmula 1. Iremos responder às seguintes perguntas:

1. [Qual piloto chegou mais vezes em 2º lugar?](#3.1.-Qual-piloto-chegou-mais-vezes-em-2º-lugar?)
2. [Quais pilotos ganharam mais posições em relação à posição de largada?](#3.2.-Quais-pilotos-ganharam-mais-posições-em-relação-à-posição-de-largada?)
3. [Qual temporada teve mais abandonos de carros durante as corridas?](#3.3.-Qual-temporada-teve-mais-abandonos-de-carros-durante-as-corridas?)
4. [Qual numeração fixa foi utilizada por cada piloto?](#3.4.-Qual-numeração-fixa-foi-utilizada-por-cada-piloto?)
5. [Qual equipe teve mais pódios no GP do Brasil?](#3.5.-Qual-equipe-teve-mais-pódios-no-GP-do-Brasil?)

## 2. Metodologia

Nesta seção será apresentado todo o processo de preparação, organização e limpeza de dados feito no datasets que possuem os seguintes dados:

| Coluna        | Descrição                                           |    
|---------------|-----------------------------------------------------|
| Track         | Nome da pista onde ocorreu a corrida.               |
| Position      | Posição final do piloto na corrida.                 |
| No            | Número do piloto.                                   |
| Driver        | Nome do piloto.                                     |
| Team          | Nome da equipe.                                     |
| Starting Grid | Posição de largada do piloto.                       |
| Laps          | Número de voltas completadas pelo piloto na corrida.|
| Time/Retired  | Tempo total de corrida ou motivo de desistência.    |
| Points        | Número de pontos ganhos na corrida.                 |
| +1 Pt         | Indicador de se o piloto recebeu um ponto extra.    |
| Fastest Lap   | Tempo da volta mais rápida do piloto na corrida.    |

### OBSERVAÇÕES

`+1 Pt` está representado por Yes ou No, porém só foi inserido na fórmula 1 na temporada 2021 e 2022, nesse caso não iremos utilizar essa coluna para nossa análise.

### 2.1. Importando o pandas


```python
import pandas as pd
```

### 2.2. Leitura dos Dados


```python
dataT2013 = pd.read_csv('./data/Formula1_2013season_raceResults.csv')
dataT2014 = pd.read_csv('./data/Formula1_2014season_raceResults.csv')
dataT2015 = pd.read_csv('./data/Formula1_2015season_raceResults.csv')
dataT2016 = pd.read_csv('./data/Formula1_2016Season_RaceResults.csv')
dataT2017 = pd.read_csv('./data/Formula1_2017Season_RaceResults.csv')
dataT2018 = pd.read_csv('./data/Formula1_2018Season_RaceResults.csv')
dataT2019 = pd.read_csv('./data/formula1_2019season_raceResults.csv')
dataT2020 = pd.read_csv('./data/formula1_2020season_raceResults.csv')
dataT2021 = pd.read_csv('./data/formula1_2021season_raceResults.csv')
dataT2022 = pd.read_csv('./data/Formula1_2022season_raceResults.csv')
```

### 2.3 Adicionar coluna 'Season' nos dataframes, representando os anos respectivos


```python
dataT2013['Season'] = '2013'
dataT2014['Season'] = '2014'
dataT2015['Season'] = '2015'
dataT2016['Season'] = '2016'
dataT2017['Season'] = '2017'
dataT2018['Season'] = '2018'
dataT2019['Season'] = '2019'
dataT2020['Season'] = '2020'
dataT2021['Season'] = '2021'
dataT2022['Season'] = '2022'
```

### 2.4 Renomear coluna 'Total/Gap/Retirement' em dataT2019 e dataT2020 para 'Time/Retired' para ficar padrão com os demais datasets


```python
dataT2019.rename(columns={'Total Time/Gap/Retirement': 'Time/Retired'}, inplace = True)
dataT2020.rename(columns={'Total Time/Gap/Retirement': 'Time/Retired'}, inplace = True)
```

### 2.5 Remover a coluna '+1 Pt' em dataT2021 e dataT2022


```python
dataT2021 = dataT2021.drop('+1 Pt', axis='columns')
dataT2022 = dataT2022.drop('+1 Pt', axis='columns')
```

### 2.6 Juntar todos datasets em apens um dataset


```python
dataFull = pd.concat([dataT2013, dataT2014, dataT2015, dataT2016, dataT2017, dataT2018, dataT2019, dataT2020, dataT2021, dataT2022])
```

### 2.7 Renomear colunas para português


```python
dataFull.columns = ['País', 'Posição', 'Nº do Piloto', 'Piloto', 'Equipe', 'Posição de largada', 'Voltas', 'Tempo/Abandono', 'Pontos', 'Volta mais rápida', 'Temporada']
```

# 3. Análise dos Dados

### 3.1. Qual piloto chegou mais vezes em 2º lugar?


```python
# Substituindo valores não numéricos da coluna 'Posição' para o numeral '0' e convertemos para inteiro
dataFull['Posição'] = dataFull['Posição'].replace('NC', 0).replace('EX', 0).replace('DQ', 0).astype('int64')

# Obtemos nosso resultado com o piloto que chegou mais vezes em 2° lugar
count_2nd_place = dataFull[dataFull['Posição'] == 2]['Piloto'].value_counts()

driver_max_2nd_place = count_2nd_place.idxmax()

print("O piloto que chegou mais vezes em 2° lugar foi", driver_max_2nd_place)
```

    O piloto que chegou mais vezes em 2° lugar foi Lewis Hamilton


### 3.2. Quais pilotos ganharam mais posições em relação à posição de largada?


```python
# Criamos uma coluna 'Ganho de Posição'
dataFull['Ganho de Posição'] = dataFull['Posição de largada'] - dataFull['Posição']

# Transformamos valores não numéricos em '0' e transformamos a coluna como inteiro
dataFull['Ganho de Posição'] = dataFull['Ganho de Posição'].fillna(0).astype('int64')

# Agrupamos os pilotos e somamos e ordenamos como decrescente
result = dataFull.groupby('Piloto')['Ganho de Posição'].sum()
result = result.sort_values(ascending=False)

# Obtemos nosso resultado como os pilotos que mais ganharam posições
print("Pilotos que ganharam mais posições:\n")
print(result.head(5))
```

    Pilotos que ganharam mais posições:
    
    Piloto
    Fernando Alonso    644
    Romain Grosjean    625
    Marcus Ericsson    604
    Carlos Sainz       549
    Lance Stroll       511
    Name: Ganho de Posição, dtype: int64


### 3.3. Qual temporada teve mais abandonos de carros durante as corridas?


```python
dropouts = dataFull[dataFull['Tempo/Abandono'] == 'DNF']

# Agrupamos os dados por temporada e contamos o número de abandonos
dropouts_count = dropouts.groupby('Temporada').size()

# Identificar a temporada com o maior número de abandonos
session_max_dropouts = dropouts_count.idxmax()

print("Temporada com o mais abandonos:", session_max_dropouts)
print("Número de abandonos:", dropouts_count[session_max_dropouts])
```

    Temporada com o mais abandonos: 2017
    Número de abandonos: 92


### 3.4. Qual numeração fixa foi utilizada por cada piloto?


```python
fixed_numbering = dataFull.groupby('Piloto')['Nº do Piloto'].unique()

# Imprimimos a numeração fixa de cada piloto
for driver, numbering in fixed_numbering.items():
    print("Piloto:", driver)
    print("Numeração Fixa:", numbering)
    print()
```

    Piloto: Adrian Sutil
    Numeração Fixa: [15 99]
    
    Piloto: Alexander Albon
    Numeração Fixa: [23]
    
    Piloto: Alexander Rossi
    Numeração Fixa: [53]
    
    Piloto: Andre Lotterer
    Numeração Fixa: [45]
    
    Piloto: Antonio Giovinazzi
    Numeração Fixa: [36 99]
    
    Piloto: Brendon Hartley
    Numeração Fixa: [39 28]
    
    Piloto: Carlos Sainz
    Numeração Fixa: [55]
    
    Piloto: Charles Leclerc
    Numeração Fixa: [16]
    
    Piloto: Charles Pic
    Numeração Fixa: [20]
    
    Piloto: Daniel Ricciardo
    Numeração Fixa: [19  3]
    
    Piloto: Daniil Kvyat
    Numeração Fixa: [26]
    
    Piloto: Daniil Kyvat
    Numeração Fixa: [26]
    
    Piloto: Esteban Gutierrez
    Numeração Fixa: [12 21]
    
    Piloto: Esteban Ocon
    Numeração Fixa: [31]
    
    Piloto: Felipe Massa
    Numeração Fixa: [ 4 19]
    
    Piloto: Felipe Nasr
    Numeração Fixa: [12]
    
    Piloto: Fernando Alonso
    Numeração Fixa: [ 3 14]
    
    Piloto: George Russell
    Numeração Fixa: [63]
    
    Piloto: Giedo van der Garde
    Numeração Fixa: [21]
    
    Piloto: Guanyu Zhou
    Numeração Fixa: [24]
    
    Piloto: Heikki Kovalainen
    Numeração Fixa: [7]
    
    Piloto: Jack Aitken
    Numeração Fixa: [89]
    
    Piloto: Jean-Eric Vergne
    Numeração Fixa: [18 25]
    
    Piloto: Jenson Button
    Numeração Fixa: [ 5 22]
    
    Piloto: Jolyon Palmer
    Numeração Fixa: [30]
    
    Piloto: Jules Bianchi
    Numeração Fixa: [22 17]
    
    Piloto: Kamui Kobayashi
    Numeração Fixa: [10]
    
    Piloto: Kevin Magnussen
    Numeração Fixa: [20]
    
    Piloto: Kimi Raikkönen
    Numeração Fixa: [7]
    
    Piloto: Lance Stroll
    Numeração Fixa: [18]
    
    Piloto: Lando Norris
    Numeração Fixa: [4]
    
    Piloto: Lewis Hamilton
    Numeração Fixa: [10 44]
    
    Piloto: Marcus Ericsson
    Numeração Fixa: [9]
    
    Piloto: Mark Webber
    Numeração Fixa: [2]
    
    Piloto: Max Chilton
    Numeração Fixa: [23  4]
    
    Piloto: Max Verstappen
    Numeração Fixa: [33  1]
    
    Piloto: Mick Schumacher
    Numeração Fixa: [47]
    
    Piloto: Nicholas Latifi
    Numeração Fixa: [6]
    
    Piloto: Nico Hulkenberg
    Numeração Fixa: [11 27]
    
    Piloto: Nico Rosberg
    Numeração Fixa: [9 6]
    
    Piloto: Nikita Mazepin
    Numeração Fixa: [9]
    
    Piloto: Nyck De Vries
    Numeração Fixa: [45]
    
    Piloto: Pascal Wehrlein
    Numeração Fixa: [94]
    
    Piloto: Pastor Maldonado
    Numeração Fixa: [16 13]
    
    Piloto: Paul di Resta
    Numeração Fixa: [14 40]
    
    Piloto: Pierre Gasly
    Numeração Fixa: [10]
    
    Piloto: Pietro Fittipaldi
    Numeração Fixa: [51]
    
    Piloto: Rio Haryanto
    Numeração Fixa: [88]
    
    Piloto: Robert Kubica
    Numeração Fixa: [88]
    
    Piloto: Roberto Merhi
    Numeração Fixa: [98]
    
    Piloto: Romain Grosjean
    Numeração Fixa: [8]
    
    Piloto: Sebastian Vettel
    Numeração Fixa: [1 5]
    
    Piloto: Sergey Sirotkin
    Numeração Fixa: [35]
    
    Piloto: Sergio Perez
    Numeração Fixa: [ 6 11]
    
    Piloto: Stoffel Vandoorne
    Numeração Fixa: [47  2]
    
    Piloto: Valtteri Bottas
    Numeração Fixa: [17 77]
    
    Piloto: Will Stevens
    Numeração Fixa: [46 28]
    
    Piloto: Yuki Tsunoda
    Numeração Fixa: [22]
    


### 3.5. Qual equipe teve mais pódios no GP do Brasil?


```python
# Filtramos apenas os registros do GP do Brasil
brazil_gp = dataFull[dataFull['País'] == 'Brazil']

# Agrupamos os dados pela equipe e contamos o número de pódios
podium_count = brazil_gp.groupby('Equipe').size()

# Identificamos a equipe com o maior número de pódios
team_max_podios = podium_count.idxmax()

print("Equipe com mais pódios no Grande Prêmio do Brasil:", team_max_podios)
print("Número de pódios:", podium_count[team_max_podios])
```

    Equipe com mais pódios no Grande Prêmio do Brasil: Ferrari
    Número de pódios: 18


## REFERÊNCIAS

- [Github @toUpperCase78 - formula1-datasets](https://github.com/toUpperCase78/formula1-datasets)
- [Análise de Dados Exploratória do Titanic](https://github.com/profdiegoaugusto/analise-dados/blob/master/Pandas/titanic-eda/titanic_an%C3%A1lise_explorat%C3%B3ria.ipynb)
