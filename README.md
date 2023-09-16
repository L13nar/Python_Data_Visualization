# Python_data_visualization
Проект по визуализации преступлений в США: загрузка, очистка данных, группировка по штатам и визуализация на интерактивной карте для анализа преступлений в разные годы.

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

df = pd.read_csv('fatal-police-shootings-data.csv')
df.head()

df3 = df.drop(columns=['id', 'flee_status', 'location_precision','race_source','body_camera', 'agency_ids','was_mental_illness_related','threat_type','county'])

df3['race']=df3['race'].replace({'A': 'Asian', 'B': 'Black', 'H': 'Latino', 'N': 'Native', 'O': 'Other', 'W': 'White', 'B:H': 'Black'})

df3 = df3.dropna(subset=['race'])
filtered_rows = df3[df3['race'].isna()]
print(filtered_rows)

#Empty DataFrame
#Columns: [date, armed_with, city, state, latitude, longitude, name, age, gender, race]
#Index: []

count_missing_race = df3['race'].isna().sum()
print("Количество строк с пустой колонкой 'race':", count_missing_race)

#Количество строк с пустой колонкой 'race': 0

df_trimmed = df3[df3['date'] <= '2022-12-31']

#Найти индекс строки, где 'race' равно 'B;H'
index_to_drop = df_trimmed[df_trimmed['race'] == 'B;H'].index

#Удалить эту строку из DataFrame
df_trimmed = df_trimmed.drop(index_to_drop)

max_date = df_trimmed['date'].max()
print(max_date)

#2022-12-31

import matplotlib.pyplot as plt

#Преобразование колонки 'date' в тип datetime
df_trimmed ['date'] = pd.to_datetime(df_trimmed ['date'])

#Извлечение года из даты
df_trimmed ['year'] = df_trimmed ['date'].dt.year

#Группировка данных по годам и подсчет количества преступлений
crime_counts_by_year = df_trimmed ['year'].value_counts().sort_index()

## Построение линейного графика 'Количества преступлений по годам'
plt.figure(figsize=(12, 6))
plt.plot(crime_counts_by_year.index, crime_counts_by_year.values, marker='o', linestyle='-')
plt.title('График количества преступлений по годам')
plt.xlabel('Год')
plt.ylabel('Количество преступлений')
plt.grid(True)
plt.show()

![](https://github.com/L13nar/Python_data_visualization/blob/main/График-количества-преступлений-по-годам.png)

#Создание словаря с цветами для каждой расы
colors = {
    'Asian': 'yellow',
    'Black': 'black',
    'Latino': 'brown',
    'Native': 'red',
    'Other': 'green',
    'White': 'blue'}
    
#Группировка данных по расам и подсчет количества преступлений

crime_counts_by_race = df_trimmed['race'].value_counts()

## Построение гистограммы "Гистограмма количества преступлений по расам (до 2022 года)"
plt.figure(figsize=(12, 6))
colors_to_use = [colors.get(race, 'gray') for race in crime_counts_by_race.index]
ax = crime_counts_by_race.plot(kind='bar', color=colors_to_use)
plt.title('Гистограмма количества преступлений по расам (до 2022 года)')
plt.xlabel('Race')
plt.ylabel('Количество преступлений')
plt.grid(True)
#Добавление подписей с количеством преступлений к столбцам
for i, count in enumerate(crime_counts_by_race):
    ax.annotate(str(count), (i, count), ha='center', va='bottom')
plt.show()

![](https://github.com/L13nar/Python_data_visualization/blob/main/Гистограмма-количества-преступлений-по-расам-_до-2022-года_.png)

crime_counts_by_race

grouped_data = df_trimmed.groupby(['year', 'race']).size().reset_index(name='crimes')
grouped_data.head()

import pandas as pd
import plotly.express as px
df = df_trimmed
#Фильтрация данных для 2020, 2021 и 2022 годов
df_2020_2022 = df[(df['year'] >= 2020) & (df['year'] <= 2022)]
#Группировка данных по штатам и подсчет преступлений
crime_by_state = df_2020_2022.groupby('state').size().reset_index(name='crime_count')

## Создание интерактивной карты "Преступления в 2020-2022 годах по штатам США"
fig = px.choropleth(locations=crime_by_state['state'], locationmode="USA-states", color=crime_by_state['crime_count'],
                    scope="usa", color_continuous_scale="Viridis",
                    title="Преступления в 2020-2022 годах по штатам")
fig.update_geos(fitbounds="locations")
fig.show()

![](https://github.com/L13nar/Python_data_visualization/blob/main/Преступления%20в%202020-2022%20годах%20по%20штатам.png)

![](https://github.com/L13nar/Python_data_visualization/blob/main/Гистограмма-количества-преступлений-по-расам-_до-2022-года_.png)
