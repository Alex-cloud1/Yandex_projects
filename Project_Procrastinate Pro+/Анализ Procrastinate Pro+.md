# Проект


# Описание проекта:

 - Нам представлены данные о развлекательном приложении Procrastinate Pro+ о пользователях, привлечённых с 1 мая по 27 октября 2019 года: лог сервера с данными об их посещениях, выгрузка их покупок за этот период,рекламные расходы. Несмотря на огромные вложения в рекламу, последние несколько месяцев компания терпит убытки.


# Цель исследования:

 - Разобраться в причинах, почему последние несколько месяцев компания терпит убыткии и помочь компании выйти в плюс.


# Задачи исследования:

 1. изучить откуда приходят пользователи и какими устройствами они пользуются;
 2. выявить сколько стоит привлечение пользователей из различных рекламных каналов;
 3. посчитать сколько денег приносит каждый клиент;
 4. понять когда расходы на привлечение клиента окупаются;
 5. исследовать какие факторы мешают привлечению клиентов.
 
 
# План действий:

1. Подготовка данных:

  - Загрузить данные и изучить общую информацию о них.
  - Провести предобработку данных: заменить названия столбцов на более удобные, преобразовать данные в нужные типы, обработать пропуски при необходимости.
  
  
2. Задать функции для расчёта и анализа LTV, ROI, удержания и конверсии:

 - функции для вычисления значений метрик;
 - функции для построения графиков.
 
 
3. Исследовательский анализ данных:

 - Составить профили пользователей. Определить минимальную и максимальную даты привлечения пользователей;
 - Выяснить, из каких стран пользователи приходят в приложение и на какую страну приходится больше всего платящих пользователей. Построить таблицу, отражающую количество пользователей и долю платящих из каждой страны;
 - Узнать, какими устройствами пользуются клиенты и какие устройства предпочитают платящие пользователи. Построить таблицу, отражающую количество пользователей и долю платящих для каждого устройства;
 - Изучить рекламные источники привлечения и определите каналы, из которых пришло больше всего платящих пользователей. Построить таблицу, отражающую количество пользователей и долю платящих для каждого канала привлечения.
 

4. Маркетинг:

 - Посчитать общую сумму расходов на маркетинг;
 - Выяснить, как траты распределены по рекламным источникам, то есть сколько денег потратили на каждый источник;
 - Построить график с визуализацией динамики изменения расходов во времени по неделям по каждому источнику. Затем на другом графике визуализировать динамику изменения расходов во времени по месяцам по каждому источнику;
 - Узнать, сколько в среднем стоило привлечение одного пользователя (CAC) из каждого источника.
 
 
5. Оценить окупаемость рекламы:

 - Проанализировать окупаемость рекламы c помощью графиков LTV и ROI, а также графики динамики LTV, CAC и ROI;
 - Проверить конверсию пользователей и динамику её изменения. То же самое сделать с удержанием пользователей. Построить и изучите графики конверсии и удержани;
 - Проанализировать окупаемость рекламы с разбивкой по устройствам. Построить графики LTV и ROI, а также графики динамики LTV, CAC и ROI;
 - Проанализировать окупаемость рекламы с разбивкой по странам. Построить графики LTV и ROI, а также графики динамики LTV, CAC и ROI.
 - Проанализировать окупаемость рекламы с разбивкой по рекламным каналам. Построить графики LTV и ROI, а также графики динамики LTV, CAC и ROI.


6. Написать вывод:

 - Выделить причины неэффективности привлечения пользователей;
 - Сформулировать рекомендации для отдела маркетинга.

## Загрузка данных и подготовка к анализу

Загрузите данные о визитах, заказах и рекламных расходах из CSV-файлов в переменные.

**Пути к файлам**

- визиты: `/datasets/visits_info_short.csv`. [Скачать датасет](https://code.s3.yandex.net/datasets/visits_info_short.csv);
- заказы: `/datasets/orders_info_short.csv`. [Скачать датасет](https://code.s3.yandex.net/datasets/orders_info_short.csv);
- расходы: `/datasets/costs_info_short.csv`. [Скачать датасет](https://code.s3.yandex.net/datasets/costs_info_short.csv).

Изучите данные и выполните предобработку. Есть ли в данных пропуски и дубликаты? Убедитесь, что типы данных во всех колонках соответствуют сохранённым в них значениям. Обратите внимание на столбцы с датой и временем.


```python
#Загрузим библиотеки

import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
import seaborn as sns
from math import factorial
from datetime import datetime, timedelta
from scipy.stats import poisson
from scipy.stats import binom
from scipy import stats as st
```


```python
#Загрузим данные в переменные

visits = pd.read_csv('/datasets/visits_info_short.csv')
orders = pd.read_csv('/datasets/orders_info_short.csv')
costs = pd.read_csv('/datasets/costs_info_short.csv')
```


```python
#Посмотрим общую информацию

display(visits.info(), orders.info(), costs.info())

```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 309901 entries, 0 to 309900
    Data columns (total 6 columns):
     #   Column         Non-Null Count   Dtype 
    ---  ------         --------------   ----- 
     0   User Id        309901 non-null  int64 
     1   Region         309901 non-null  object
     2   Device         309901 non-null  object
     3   Channel        309901 non-null  object
     4   Session Start  309901 non-null  object
     5   Session End    309901 non-null  object
    dtypes: int64(1), object(5)
    memory usage: 14.2+ MB
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 40212 entries, 0 to 40211
    Data columns (total 3 columns):
     #   Column    Non-Null Count  Dtype  
    ---  ------    --------------  -----  
     0   User Id   40212 non-null  int64  
     1   Event Dt  40212 non-null  object 
     2   Revenue   40212 non-null  float64
    dtypes: float64(1), int64(1), object(1)
    memory usage: 942.6+ KB
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 1800 entries, 0 to 1799
    Data columns (total 3 columns):
     #   Column   Non-Null Count  Dtype  
    ---  ------   --------------  -----  
     0   dt       1800 non-null   object 
     1   Channel  1800 non-null   object 
     2   costs    1800 non-null   float64
    dtypes: float64(1), object(2)
    memory usage: 42.3+ KB



    None



    None



    None


Пропусков не обнаружено.
Видим, что в столбцах Session Start,Session End, Event Dt, dt нужно перевести данные в формат datetime.


```python
#Посмотрим на первые 5 строк в каждом датафрейме.

display(visits.head(),orders.head(),costs.head())

```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>User Id</th>
      <th>Region</th>
      <th>Device</th>
      <th>Channel</th>
      <th>Session Start</th>
      <th>Session End</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>981449118918</td>
      <td>United States</td>
      <td>iPhone</td>
      <td>organic</td>
      <td>2019-05-01 02:36:01</td>
      <td>2019-05-01 02:45:01</td>
    </tr>
    <tr>
      <th>1</th>
      <td>278965908054</td>
      <td>United States</td>
      <td>iPhone</td>
      <td>organic</td>
      <td>2019-05-01 04:46:31</td>
      <td>2019-05-01 04:47:35</td>
    </tr>
    <tr>
      <th>2</th>
      <td>590706206550</td>
      <td>United States</td>
      <td>Mac</td>
      <td>organic</td>
      <td>2019-05-01 14:09:25</td>
      <td>2019-05-01 15:32:08</td>
    </tr>
    <tr>
      <th>3</th>
      <td>326433527971</td>
      <td>United States</td>
      <td>Android</td>
      <td>TipTop</td>
      <td>2019-05-01 00:29:59</td>
      <td>2019-05-01 00:54:25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>349773784594</td>
      <td>United States</td>
      <td>Mac</td>
      <td>organic</td>
      <td>2019-05-01 03:33:35</td>
      <td>2019-05-01 03:57:40</td>
    </tr>
  </tbody>
</table>
</div>



<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>User Id</th>
      <th>Event Dt</th>
      <th>Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>188246423999</td>
      <td>2019-05-01 23:09:52</td>
      <td>4.99</td>
    </tr>
    <tr>
      <th>1</th>
      <td>174361394180</td>
      <td>2019-05-01 12:24:04</td>
      <td>4.99</td>
    </tr>
    <tr>
      <th>2</th>
      <td>529610067795</td>
      <td>2019-05-01 11:34:04</td>
      <td>4.99</td>
    </tr>
    <tr>
      <th>3</th>
      <td>319939546352</td>
      <td>2019-05-01 15:34:40</td>
      <td>4.99</td>
    </tr>
    <tr>
      <th>4</th>
      <td>366000285810</td>
      <td>2019-05-01 13:59:51</td>
      <td>4.99</td>
    </tr>
  </tbody>
</table>
</div>



<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>dt</th>
      <th>Channel</th>
      <th>costs</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2019-05-01</td>
      <td>FaceBoom</td>
      <td>113.3</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2019-05-02</td>
      <td>FaceBoom</td>
      <td>78.1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2019-05-03</td>
      <td>FaceBoom</td>
      <td>85.8</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2019-05-04</td>
      <td>FaceBoom</td>
      <td>136.4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2019-05-05</td>
      <td>FaceBoom</td>
      <td>122.1</td>
    </tr>
  </tbody>
</table>
</div>


В данных нужно привести к общему формату - названия к нижнему регистру и убрать пробелы.


```python
#Теперь посмотрим на дубликаты.

display(visits.duplicated().sum(), orders.duplicated().sum(), costs.duplicated().sum())


```


    0



    0



    0


Явных дубликатов не обнаружено. Перейдем к предобработке данных.

## Предобработка данных


```python
#Переименуем столбцы.

visits.rename(columns = {'User Id':'user_id', 'Region':'region', 'Device':'device', 'Channel':'channel',
                        'Session Start':'session_start', 'Session End':'session_end'}, inplace = True)
orders.rename(columns = {'User Id':'user_id', 'Event Dt':'event_dt', 'Revenue':'revenue'}, inplace = True)
costs.rename(columns = {'Channel':'channel'}, inplace = True)

display(visits.head(), orders.head(), costs.head())

```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>user_id</th>
      <th>region</th>
      <th>device</th>
      <th>channel</th>
      <th>session_start</th>
      <th>session_end</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>981449118918</td>
      <td>United States</td>
      <td>iPhone</td>
      <td>organic</td>
      <td>2019-05-01 02:36:01</td>
      <td>2019-05-01 02:45:01</td>
    </tr>
    <tr>
      <th>1</th>
      <td>278965908054</td>
      <td>United States</td>
      <td>iPhone</td>
      <td>organic</td>
      <td>2019-05-01 04:46:31</td>
      <td>2019-05-01 04:47:35</td>
    </tr>
    <tr>
      <th>2</th>
      <td>590706206550</td>
      <td>United States</td>
      <td>Mac</td>
      <td>organic</td>
      <td>2019-05-01 14:09:25</td>
      <td>2019-05-01 15:32:08</td>
    </tr>
    <tr>
      <th>3</th>
      <td>326433527971</td>
      <td>United States</td>
      <td>Android</td>
      <td>TipTop</td>
      <td>2019-05-01 00:29:59</td>
      <td>2019-05-01 00:54:25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>349773784594</td>
      <td>United States</td>
      <td>Mac</td>
      <td>organic</td>
      <td>2019-05-01 03:33:35</td>
      <td>2019-05-01 03:57:40</td>
    </tr>
  </tbody>
</table>
</div>



<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>user_id</th>
      <th>event_dt</th>
      <th>revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>188246423999</td>
      <td>2019-05-01 23:09:52</td>
      <td>4.99</td>
    </tr>
    <tr>
      <th>1</th>
      <td>174361394180</td>
      <td>2019-05-01 12:24:04</td>
      <td>4.99</td>
    </tr>
    <tr>
      <th>2</th>
      <td>529610067795</td>
      <td>2019-05-01 11:34:04</td>
      <td>4.99</td>
    </tr>
    <tr>
      <th>3</th>
      <td>319939546352</td>
      <td>2019-05-01 15:34:40</td>
      <td>4.99</td>
    </tr>
    <tr>
      <th>4</th>
      <td>366000285810</td>
      <td>2019-05-01 13:59:51</td>
      <td>4.99</td>
    </tr>
  </tbody>
</table>
</div>



<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>dt</th>
      <th>channel</th>
      <th>costs</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2019-05-01</td>
      <td>FaceBoom</td>
      <td>113.3</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2019-05-02</td>
      <td>FaceBoom</td>
      <td>78.1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2019-05-03</td>
      <td>FaceBoom</td>
      <td>85.8</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2019-05-04</td>
      <td>FaceBoom</td>
      <td>136.4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2019-05-05</td>
      <td>FaceBoom</td>
      <td>122.1</td>
    </tr>
  </tbody>
</table>
</div>


Успешно поменяли названия столбцов.


```python
#Теперь поменяем тип данных в столбцах с датой.

visits['session_start'] = pd.to_datetime(visits['session_start'])
visits['session_end'] = pd.to_datetime(visits['session_end'])
orders['event_dt'] = pd.to_datetime(orders['event_dt'])
costs['dt'] = pd.to_datetime(costs['dt']).dt.date 
```

## Функции для расчёта и анализа LTV, ROI, удержания и конверсии.

Это функции для вычисления значений метрик:

- `get_profiles()` — для создания профилей пользователей,
- `get_retention()` — для подсчёта Retention Rate,
- `get_conversion()` — для подсчёта конверсии,
- `get_ltv()` — для подсчёта LTV.

А также функции для построения графиков:

- `filter_data()` — для сглаживания данных,
- `plot_retention()` — для построения графика Retention Rate,
- `plot_conversion()` — для построения графика конверсии,
- `plot_ltv_roi` — для визуализации LTV и ROI.

### Функция для создания профилей пользователей


```python
#Зададим функцию get_profiles() — для создания профилей пользователей

def get_profiles(visits, orders, costs):
    
    profiles = (
        visits.sort_values(by=['user_id', 'session_start'])
        .groupby('user_id')
        .agg(
            {
                'session_start': 'first',
                'channel': 'first',
                'device': 'first',
                'region': 'first',
            }
        )
        .rename(columns={'session_start': 'first_ts'})
         .reset_index()
    )
    
# Определяем дату первого посещения
# И первый день месяца, в который это посещение произошло

    profiles['dt'] = profiles['first_ts'].dt.date
    profiles['month'] = profiles['first_ts'].astype('datetime64[M]')
    
#Проверьте, есть ли ID пользователей из profiles в orders

    profiles['payer'] = profiles['user_id'].isin(orders['user_id'].unique())
    
#Считаем количество уникальных пользователей с одинаковыми источником и датой привлечения

    new_users = (
        profiles.groupby(['dt', 'channel'])
        .agg({'user_id': 'nunique'})
        .rename(columns={'user_id': 'unique_users'})
        .reset_index()
    )
    
#Объединяем траты на рекламу и число привлечённых пользователей

    costs = costs.merge(new_users, on=['dt', 'channel'], how='left')
    
#Делим рекламные расходы на число привлечённых пользователей

    costs['acquisition_cost'] = costs['costs'] / costs['unique_users']

#Добавляем стоимость привлечения в профили

    profiles = profiles.merge(
        costs[['dt', 'channel', 'acquisition_cost']],
        on=['dt', 'channel'],
        how='left',
    )

#Стоимость привлечения органических пользователей равна нулю

    profiles['acquisition_cost'] = profiles['acquisition_cost'].fillna(0)

    return profiles


```

### Функция для подсчёта Retention Rate


```python
def get_retention(
    profiles,
    visits,
    observation_date,
    horizon_days,
    dimensions=[],
    ignore_horizon=False,
):

#Добавляем столбец payer в передаваемый dimensions список

    dimensions = ['payer'] + dimensions

#Исключаем пользователей, не «доживших» до горизонта анализа

    last_suitable_acquisition_date = observation_date
    if not ignore_horizon:
        last_suitable_acquisition_date = observation_date - timedelta(
            days=horizon_days - 1
        )
    result_raw = profiles.query('dt <= @last_suitable_acquisition_date')

#Собираем «сырые» данные для расчёта удержания

    result_raw = result_raw.merge(
        visits[['user_id', 'session_start']], on='user_id', how='left'
    )
    result_raw['lifetime'] = (
        result_raw['session_start'] - result_raw['first_ts']
    ).dt.days

#Функция для группировки таблицы по желаемым признакам

    def group_by_dimensions(df, dims, horizon_days):
        result = df.pivot_table(
            index=dims, columns='lifetime', values='user_id', aggfunc='nunique'
        )
        cohort_sizes = (
            df.groupby(dims)
            .agg({'user_id': 'nunique'})
            .rename(columns={'user_id': 'cohort_size'})
        )
        result = cohort_sizes.merge(result, on=dims, how='left').fillna(0)
        result = result.div(result['cohort_size'], axis=0)
        result = result[['cohort_size'] + list(range(horizon_days))]
        result['cohort_size'] = cohort_sizes
        return result

#Получаем таблицу удержания

    result_grouped = group_by_dimensions(result_raw, dimensions, horizon_days)

#Получаем таблицу динамики удержания

    result_in_time = group_by_dimensions(
        result_raw, dimensions + ['dt'], horizon_days
    )

#Возвращаем обе таблицы и сырые данные

    return result_raw, result_grouped, result_in_time 
```

### Функция для подсчета конверсии


```python
def get_conversion(
    profiles,
    orders,
    observation_date,
    horizon_days,
    dimensions=[],
    ignore_horizon=False,
):

#Исключаем пользователей, не «доживших» до горизонта анализа

    last_suitable_acquisition_date = observation_date
    if not ignore_horizon:
        last_suitable_acquisition_date = observation_date - timedelta(
            days=horizon_days - 1
        )
    result_raw = profiles.query('dt <= @last_suitable_acquisition_date')

#Определяем дату и время первой покупки для каждого пользователя

    first_orders = (
        orders.sort_values(by=['user_id', 'event_dt'])
        .groupby('user_id')
        .agg({'event_dt': 'first'})
        .reset_index()
    )

#Добавляем данные о покупках в профили

    result_raw = result_raw.merge(
        first_orders[['user_id', 'event_dt']], on='user_id', how='left'
    )

#Рассчитываем лайфтайм для каждой покупки

    result_raw['lifetime'] = (
        result_raw['event_dt'] - result_raw['first_ts']
    ).dt.days

#Группируем по cohort, если в dimensions ничего нет

    if len(dimensions) == 0:
        result_raw['cohort'] = 'All users' 
        dimensions = dimensions + ['cohort']

#Функция для группировки таблицы по желаемым признакам

    def group_by_dimensions(df, dims, horizon_days):
        result = df.pivot_table(
            index=dims, columns='lifetime', values='user_id', aggfunc='nunique'
        )
        result = result.fillna(0).cumsum(axis = 1)
        cohort_sizes = (
            df.groupby(dims)
            .agg({'user_id': 'nunique'})
            .rename(columns={'user_id': 'cohort_size'})
        )
        result = cohort_sizes.merge(result, on=dims, how='left').fillna(0)
        
#Делим каждую «ячейку» в строке на размер когорты и получаем conversion rate

        result = result.div(result['cohort_size'], axis=0)
        result = result[['cohort_size'] + list(range(horizon_days))]
        result['cohort_size'] = cohort_sizes
        return result

#Получаем таблицу конверсии

    result_grouped = group_by_dimensions(result_raw, dimensions, horizon_days)

#Для таблицы динамики конверсии убираем 'cohort' из dimensions

    if 'cohort' in dimensions: 
        dimensions = []

#Получаем таблицу динамики конверсии

    result_in_time = group_by_dimensions(
        result_raw, dimensions + ['dt'], horizon_days
    )

#Возвращаем обе таблицы и сырые данные

    return result_raw, result_grouped, result_in_time 
```

### Функция для подсчёта LTV


```python
def get_ltv(
    profiles,
    orders,
    observation_date,
    horizon_days,
    dimensions=[],
    ignore_horizon=False,
):

#Исключаем пользователей, не «доживших» до горизонта анализа
    last_suitable_acquisition_date = observation_date
    if not ignore_horizon:
        last_suitable_acquisition_date = observation_date - timedelta(
            days=horizon_days - 1
        )
    result_raw = profiles.query('dt <= @last_suitable_acquisition_date')
    
#Добавляем данные о покупках в профили

    result_raw = result_raw.merge(
        orders[['user_id', 'event_dt', 'revenue']], on='user_id', how='left'
    )
#Рассчитываем лайфтайм пользователя для каждой покупки

    result_raw['lifetime'] = (
        result_raw['event_dt'] - result_raw['first_ts']
    ).dt.days
    
#Группируем по cohort, если в dimensions ничего нет

    if len(dimensions) == 0:
        result_raw['cohort'] = 'All users'
        dimensions = dimensions + ['cohort']

#Функция группировки по желаемым признакам

    def group_by_dimensions(df, dims, horizon_days):
        # строим «треугольную» таблицу выручки
        result = df.pivot_table(
            index=dims, columns='lifetime', values='revenue', aggfunc='sum'
        )
#Находим сумму выручки с накоплением

        result = result.fillna(0).cumsum(axis=1)
    
#Вычисляем размеры когорт

        cohort_sizes = (
            df.groupby(dims)
            .agg({'user_id': 'nunique'})
            .rename(columns={'user_id': 'cohort_size'})
        )
    
#Объединяем размеры когорт и таблицу выручки

        result = cohort_sizes.merge(result, on=dims, how='left').fillna(0)
    
#Считаем LTV: делим каждую «ячейку» в строке на размер когорты

        result = result.div(result['cohort_size'], axis=0)
    
#Исключаем все лайфтаймы, превышающие горизонт анализа

        result = result[['cohort_size'] + list(range(horizon_days))]
    
#Восстанавливаем размеры когорт

        result['cohort_size'] = cohort_sizes

#Собираем датафрейм с данными пользователей и значениями CAC, добавляя параметры из dimensions

        cac = df[['user_id', 'acquisition_cost'] + dims].drop_duplicates()

#Считаем средний CAC по параметрам из dimensions

        cac = (
            cac.groupby(dims)
            .agg({'acquisition_cost': 'mean'})
            .rename(columns={'acquisition_cost': 'cac'})
        )

#Считаем ROI: делим LTV на CAC

        roi = result.div(cac['cac'], axis=0)

#Удаляем строки с бесконечным ROI

        roi = roi[~roi['cohort_size'].isin([np.inf])]

#Восстанавливаем размеры когорт в таблице ROI

        roi['cohort_size'] = cohort_sizes

#Добавляем CAC в таблицу ROI

        roi['cac'] = cac['cac']

#В финальной таблице оставляем размеры когорт, CAC и ROI в лайфтаймы, не превышающие горизонт анализа

        roi = roi[['cohort_size', 'cac'] + list(range(horizon_days))]

#Возвращаем таблицы LTV и ROI

        return result, roi

#Получаем таблицы LTV и ROI

    result_grouped, roi_grouped = group_by_dimensions(
        result_raw, dimensions, horizon_days
    )

#Для таблиц динамики убираем 'cohort' из dimensions

    if 'cohort' in dimensions:
        dimensions = []

#Получаем таблицы динамики LTV и ROI

    result_in_time, roi_in_time = group_by_dimensions(
        result_raw, dimensions + ['dt'], horizon_days
    )

    return (
        result_raw,  
        result_grouped, 
        result_in_time,  
        roi_grouped,  
        roi_in_time,  
    ) 
```

### Функция для сглаживания данных


```python
def filter_data(df, window):
    for column in df.columns.values:
        df[column] = df[column].rolling(window).mean() 
    return df 
```

### Функция для построения графика Retention Rate


```python
def plot_retention(retention, retention_history, horizon, window=7):

    # задаём размер сетки для графиков
    plt.figure(figsize=(15, 10))

    # исключаем размеры когорт и удержание первого дня
    retention = retention.drop(columns=['cohort_size', 0])
    # в таблице динамики оставляем только нужный лайфтайм
    retention_history = retention_history.drop(columns=['cohort_size'])[
        [horizon - 1]
    ]

    # если в индексах таблицы удержания только payer,
    # добавляем второй признак — cohort
    if retention.index.nlevels == 1:
        retention['cohort'] = 'All users'
        retention = retention.reset_index().set_index(['cohort', 'payer'])

    # в таблице графиков — два столбца и две строки, четыре ячейки
    # в первой строим кривые удержания платящих пользователей
    ax1 = plt.subplot(2, 2, 1)
    retention.query('payer == True').droplevel('payer').T.plot(
        grid=True, ax=ax1
    )
    plt.legend()
    plt.xlabel('Лайфтайм')
    plt.title('Удержание платящих пользователей')

    # во второй ячейке строим кривые удержания неплатящих
    # вертикальная ось — от графика из первой ячейки
    ax2 = plt.subplot(2, 2, 2, sharey=ax1)
    retention.query('payer == False').droplevel('payer').T.plot(
        grid=True, ax=ax2
    )
    plt.legend()
    plt.xlabel('Лайфтайм')
    plt.title('Удержание неплатящих пользователей')

    # в третьей ячейке — динамика удержания платящих
    ax3 = plt.subplot(2, 2, 3)
    # получаем названия столбцов для сводной таблицы
    columns = [
        name
        for name in retention_history.index.names
        if name not in ['dt', 'payer']
    ]
    # фильтруем данные и строим график
    filtered_data = retention_history.query('payer == True').pivot_table(
        index='dt', columns=columns, values=horizon - 1, aggfunc='mean'
    )
    filter_data(filtered_data, window).plot(grid=True, ax=ax3)
    plt.xlabel('Дата привлечения')
    plt.title(
        'Динамика удержания платящих пользователей на {}-й день'.format(
            horizon
        )
    )

    # в чётвертой ячейке — динамика удержания неплатящих
    ax4 = plt.subplot(2, 2, 4, sharey=ax3)
    # фильтруем данные и строим график
    filtered_data = retention_history.query('payer == False').pivot_table(
        index='dt', columns=columns, values=horizon - 1, aggfunc='mean'
    )
    filter_data(filtered_data, window).plot(grid=True, ax=ax4)
    plt.xlabel('Дата привлечения')
    plt.title(
        'Динамика удержания неплатящих пользователей на {}-й день'.format(
            horizon
        )
    )
    
    plt.tight_layout()
    plt.show() 
```

### Функция для построения графика конверсии


```python
def plot_conversion(conversion, conversion_history, horizon, window=7):

    # задаём размер сетки для графиков
    plt.figure(figsize=(15, 5))

    # исключаем размеры когорт
    conversion = conversion.drop(columns=['cohort_size'])
    # в таблице динамики оставляем только нужный лайфтайм
    conversion_history = conversion_history.drop(columns=['cohort_size'])[
        [horizon - 1]
    ]

    # первый график — кривые конверсии
    ax1 = plt.subplot(1, 2, 1)
    conversion.T.plot(grid=True, ax=ax1)
    plt.legend()
    plt.xlabel('Лайфтайм')
    plt.title('Конверсия пользователей')

    # второй график — динамика конверсии
    ax2 = plt.subplot(1, 2, 2, sharey=ax1)
    columns = [
        # столбцами сводной таблицы станут все столбцы индекса, кроме даты
        name for name in conversion_history.index.names if name not in ['dt']
    ]
    filtered_data = conversion_history.pivot_table(
        index='dt', columns=columns, values=horizon - 1, aggfunc='mean'
    )
    filter_data(filtered_data, window).plot(grid=True, ax=ax2)
    plt.xlabel('Дата привлечения')
    plt.title('Динамика конверсии пользователей на {}-й день'.format(horizon))

    plt.tight_layout()
    plt.show() 
```

### Функция для визуализации LTV и ROI


```python
def plot_ltv_roi(ltv, ltv_history, roi, roi_history, horizon, window=7):

    # задаём сетку отрисовки графиков
    plt.figure(figsize=(20, 10))

    # из таблицы ltv исключаем размеры когорт
    ltv = ltv.drop(columns=['cohort_size'])
    # в таблице динамики ltv оставляем только нужный лайфтайм
    ltv_history = ltv_history.drop(columns=['cohort_size'])[[horizon - 1]]

    # стоимость привлечения запишем в отдельный фрейм
    cac_history = roi_history[['cac']]

    # из таблицы roi исключаем размеры когорт и cac
    roi = roi.drop(columns=['cohort_size', 'cac'])
    # в таблице динамики roi оставляем только нужный лайфтайм
    roi_history = roi_history.drop(columns=['cohort_size', 'cac'])[
        [horizon - 1]
    ]

    # первый график — кривые ltv
    ax1 = plt.subplot(2, 3, 1)
    ltv.T.plot(grid=True, ax=ax1)
    plt.legend()
    plt.xlabel('Лайфтайм')
    plt.title('LTV')

    # второй график — динамика ltv
    ax2 = plt.subplot(2, 3, 2, sharey=ax1)
    # столбцами сводной таблицы станут все столбцы индекса, кроме даты
    columns = [name for name in ltv_history.index.names if name not in ['dt']]
    filtered_data = ltv_history.pivot_table(
        index='dt', columns=columns, values=horizon - 1, aggfunc='mean'
    )
    filter_data(filtered_data, window).plot(grid=True, ax=ax2)
    plt.xlabel('Дата привлечения')
    plt.title('Динамика LTV пользователей на {}-й день'.format(horizon))

    # третий график — динамика cac
    ax3 = plt.subplot(2, 3, 3, sharey=ax1)
    # столбцами сводной таблицы станут все столбцы индекса, кроме даты
    columns = [name for name in cac_history.index.names if name not in ['dt']]
    filtered_data = cac_history.pivot_table(
        index='dt', columns=columns, values='cac', aggfunc='mean'
    )
    filter_data(filtered_data, window).plot(grid=True, ax=ax3)
    plt.xlabel('Дата привлечения')
    plt.title('Динамика стоимости привлечения пользователей')

    # четвёртый график — кривые roi
    ax4 = plt.subplot(2, 3, 4)
    roi.T.plot(grid=True, ax=ax4)
    plt.axhline(y=1, color='red', linestyle='--', label='Уровень окупаемости')
    plt.legend()
    plt.xlabel('Лайфтайм')
    plt.title('ROI')

    # пятый график — динамика roi
    ax5 = plt.subplot(2, 3, 5, sharey=ax4)
    # столбцами сводной таблицы станут все столбцы индекса, кроме даты
    columns = [name for name in roi_history.index.names if name not in ['dt']]
    filtered_data = roi_history.pivot_table(
        index='dt', columns=columns, values=horizon - 1, aggfunc='mean'
    )
    filter_data(filtered_data, window).plot(grid=True, ax=ax5)
    plt.axhline(y=1, color='red', linestyle='--', label='Уровень окупаемости')
    plt.xlabel('Дата привлечения')
    plt.title('Динамика ROI пользователей на {}-й день'.format(horizon))

    plt.tight_layout()
    plt.show() 
```

Создали функции для расчета и анализа LTV, ROI, Retention Rate и Conversion Rate.

## Исследовательский анализ данных.

- Составим профили пользователей. Определите минимальную и максимальную даты привлечения пользователей.
- Выясним, из каких стран пользователи приходят в приложение и на какую страну приходится больше всего платящих пользователей. Построим таблицу, отражающую количество пользователей и долю платящих из каждой страны.
- Узнаем, какими устройствами пользуются клиенты и какие устройства предпочитают платящие пользователи. Построим таблицу, отражающую количество пользователей и долю платящих для каждого устройства.
- Изучим рекламные источники привлечения и определим каналы, из которых пришло больше всего платящих пользователей. Построим таблицу, отражающую количество пользователей и долю платящих для каждого канала привлечения.



```python
#Получим профили пользователей с помощью функции get_profiles

profiles = get_profiles(visits, orders, costs)
profiles.head(5) 

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>user_id</th>
      <th>first_ts</th>
      <th>channel</th>
      <th>device</th>
      <th>region</th>
      <th>dt</th>
      <th>month</th>
      <th>payer</th>
      <th>acquisition_cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>599326</td>
      <td>2019-05-07 20:58:57</td>
      <td>FaceBoom</td>
      <td>Mac</td>
      <td>United States</td>
      <td>2019-05-07</td>
      <td>2019-05-01</td>
      <td>True</td>
      <td>1.088172</td>
    </tr>
    <tr>
      <th>1</th>
      <td>4919697</td>
      <td>2019-07-09 12:46:07</td>
      <td>FaceBoom</td>
      <td>iPhone</td>
      <td>United States</td>
      <td>2019-07-09</td>
      <td>2019-07-01</td>
      <td>False</td>
      <td>1.107237</td>
    </tr>
    <tr>
      <th>2</th>
      <td>6085896</td>
      <td>2019-10-01 09:58:33</td>
      <td>organic</td>
      <td>iPhone</td>
      <td>France</td>
      <td>2019-10-01</td>
      <td>2019-10-01</td>
      <td>False</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>22593348</td>
      <td>2019-08-22 21:35:48</td>
      <td>AdNonSense</td>
      <td>PC</td>
      <td>Germany</td>
      <td>2019-08-22</td>
      <td>2019-08-01</td>
      <td>False</td>
      <td>0.988235</td>
    </tr>
    <tr>
      <th>4</th>
      <td>31989216</td>
      <td>2019-10-02 00:07:44</td>
      <td>YRabbit</td>
      <td>iPhone</td>
      <td>United States</td>
      <td>2019-10-02</td>
      <td>2019-10-01</td>
      <td>False</td>
      <td>0.230769</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Найдем минимальную и максимальную дату привлечения

min_date = costs['dt'].min()
max_date = costs['dt'].max()
display (min_date, max_date)
```


    datetime.date(2019, 5, 1)



    datetime.date(2019, 10, 27)



```python
#Выясним, из каких стран пользователи приходят в приложение

display(profiles['region'].unique())
```


    array(['United States', 'France', 'Germany', 'UK'], dtype=object)



```python
#На какую страну приходится больше всего платящих пользователей

users_country = profiles.query('payer == True') \
        .groupby('region') \
        .agg ({'payer': 'count'}) \
        .sort_values('payer', ascending = False)
users_country['percent'] = round(users_country['payer']* 100/ users_country['payer'].sum(),2)
users_country
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>payer</th>
      <th>percent</th>
    </tr>
    <tr>
      <th>region</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>United States</th>
      <td>6902</td>
      <td>77.72</td>
    </tr>
    <tr>
      <th>UK</th>
      <td>700</td>
      <td>7.88</td>
    </tr>
    <tr>
      <th>France</th>
      <td>663</td>
      <td>7.47</td>
    </tr>
    <tr>
      <th>Germany</th>
      <td>616</td>
      <td>6.94</td>
    </tr>
  </tbody>
</table>
</div>



Самый большой процент платящих пользователей из США - 78%.  Остальные пользователи с примерно одинаковым процентом из Великобритании, Франции и Германии.


```python
# Посмотрим таблицу, где получим процент платящих пользователей для каждой страны

profiles.groupby('region').agg({'user_id': 'nunique', 'payer': ['sum', 'mean']})
```


    ---------------------------------------------------------------------------

    NameError                                 Traceback (most recent call last)

    /tmp/ipykernel_48/98463159.py in <module>
          1 # Посмотрим таблицу, где получим процент платящих пользователей для каждой страны
          2 
    ----> 3 profiles.groupby('region').agg({'user_id': 'nunique', 'payer': ['sum', 'mean']})
    

    NameError: name 'profiles' is not defined


По этой таблице видим следующее:
- В США 7% платящих пользователй от общего числа в этой стране ; в Англии 3.9%; в Германии 4.1%; во Франции 3.7%



```python
#Построим таблицу, отражающую количество пользователей и долю платящих для каждого устройства.

users_device = profiles.query('payer == True') \
        .groupby('device') \
        .agg ({'payer': 'count'}) \
        .sort_values('payer',ascending = False)
users_device['percent'] = round(users_device['payer']* 100/ users_device['payer'].sum(),2)
users_device
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>payer</th>
      <th>percent</th>
    </tr>
    <tr>
      <th>device</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>iPhone</th>
      <td>3382</td>
      <td>38.08</td>
    </tr>
    <tr>
      <th>Android</th>
      <td>2050</td>
      <td>23.08</td>
    </tr>
    <tr>
      <th>Mac</th>
      <td>1912</td>
      <td>21.53</td>
    </tr>
    <tr>
      <th>PC</th>
      <td>1537</td>
      <td>17.31</td>
    </tr>
  </tbody>
</table>
</div>



Самый популярный девайс среди платящих пользователей -  Iphone (38%). Следом идут Android (23%), Mac (21.5%) и PC (17%).


```python
profiles.groupby('device').agg({'user_id': 'nunique', 'payer': ['sum', 'mean']})
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }

    .dataframe thead tr:last-of-type th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th>user_id</th>
      <th colspan="2" halign="left">payer</th>
    </tr>
    <tr>
      <th></th>
      <th>nunique</th>
      <th>sum</th>
      <th>mean</th>
    </tr>
    <tr>
      <th>device</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Android</th>
      <td>35032</td>
      <td>2050</td>
      <td>0.058518</td>
    </tr>
    <tr>
      <th>Mac</th>
      <td>30042</td>
      <td>1912</td>
      <td>0.063644</td>
    </tr>
    <tr>
      <th>PC</th>
      <td>30455</td>
      <td>1537</td>
      <td>0.050468</td>
    </tr>
    <tr>
      <th>iPhone</th>
      <td>54479</td>
      <td>3382</td>
      <td>0.062079</td>
    </tr>
  </tbody>
</table>
</div>



По этой таблице видим доли платящих пользователей по платформам:
- Iphone - 6.2%;
- Mac - 6.3% ;
- Android - 5,8%; 
- PC - 5% 


```python
#Построим таблицу, отражающую количество пользователей и долю платящих для каждого канала привлечения

users_channel = profiles.query('payer == True') \
        .groupby('channel') \
        .agg ({'payer': 'count'}) \
        .sort_values('payer',ascending = False)

users_channel['percent'] = round(users_channel['payer']* 100/ users_channel['payer'].sum(),2)
users_channel
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>payer</th>
      <th>percent</th>
    </tr>
    <tr>
      <th>channel</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>FaceBoom</th>
      <td>3557</td>
      <td>46.07</td>
    </tr>
    <tr>
      <th>TipTop</th>
      <td>1878</td>
      <td>24.32</td>
    </tr>
    <tr>
      <th>WahooNetBanner</th>
      <td>453</td>
      <td>5.87</td>
    </tr>
    <tr>
      <th>AdNonSense</th>
      <td>440</td>
      <td>5.70</td>
    </tr>
    <tr>
      <th>RocketSuperAds</th>
      <td>352</td>
      <td>4.56</td>
    </tr>
    <tr>
      <th>LeapBob</th>
      <td>262</td>
      <td>3.39</td>
    </tr>
    <tr>
      <th>OppleCreativeMedia</th>
      <td>233</td>
      <td>3.02</td>
    </tr>
    <tr>
      <th>lambdaMediaAds</th>
      <td>225</td>
      <td>2.91</td>
    </tr>
    <tr>
      <th>YRabbit</th>
      <td>165</td>
      <td>2.14</td>
    </tr>
    <tr>
      <th>MediaTornado</th>
      <td>156</td>
      <td>2.02</td>
    </tr>
  </tbody>
</table>
</div>



Самые популярные (Топ-3) каналы привлечение это FaceBoom (40%), TipTop (21%), organic (13%).Эти три канала приносят приносят большую часть платящих пользователей - 74%.  Далее идут каналы привлечения с процентом платящих пользователей 5% и ниже.

## Маркетинг

- Посчитайте общую сумму расходов на маркетинг.
- Выясните, как траты распределены по рекламным источникам, то есть сколько денег потратили на каждый источник.
- Постройте график с визуализацией динамики изменения расходов во времени по неделям по каждому источнику. Затем на другом графике визуализируйте динамику изменения расходов во времени по месяцам по каждому источнику.
- Узнайте, сколько в среднем стоило привлечение одного пользователя (CAC) из каждого источника. Используйте профили пользователей.

Напишите промежуточные выводы.


```python
#Посчитаем общую сумму расходов на маркетинг

costs['costs'].sum()
```




    105497.30000000002




```python
#Посмотрим сколько денег потрачено на каждый источник

costs.groupby('channel')\
    .agg({'costs' : 'sum'})\
    .sort_values('costs', ascending = False)

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>costs</th>
    </tr>
    <tr>
      <th>channel</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>TipTop</th>
      <td>54751.30</td>
    </tr>
    <tr>
      <th>FaceBoom</th>
      <td>32445.60</td>
    </tr>
    <tr>
      <th>WahooNetBanner</th>
      <td>5151.00</td>
    </tr>
    <tr>
      <th>AdNonSense</th>
      <td>3911.25</td>
    </tr>
    <tr>
      <th>OppleCreativeMedia</th>
      <td>2151.25</td>
    </tr>
    <tr>
      <th>RocketSuperAds</th>
      <td>1833.00</td>
    </tr>
    <tr>
      <th>LeapBob</th>
      <td>1797.60</td>
    </tr>
    <tr>
      <th>lambdaMediaAds</th>
      <td>1557.60</td>
    </tr>
    <tr>
      <th>MediaTornado</th>
      <td>954.48</td>
    </tr>
    <tr>
      <th>YRabbit</th>
      <td>944.22</td>
    </tr>
  </tbody>
</table>
</div>



Больше всего бюджета было потрачено на источники TipTop (54751 UE - это больше 50% от всех расходов) и FaceBoom (32445 UE). Разница почти в 2 раза. При этом FaceBoom привлекает в два раза больше платящих пользователей, чем TipTop. Далее идут каналы, на которые расходуется 5000 UE и меньше. 


```python
#Добавим новые столбцы с неделями и месяцами.

costs['dt'] = pd.to_datetime(costs['dt'])
costs['week'] = costs['dt'].dt.isocalendar().week
costs['month'] = costs['dt'].dt.month

#Посмотрим расходы по месяцам 

display (costs.groupby('month')\
        .agg ({'costs': 'sum'})\
        .sort_values('month'))
        


```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>costs</th>
    </tr>
    <tr>
      <th>month</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>5</th>
      <td>9978.570</td>
    </tr>
    <tr>
      <th>6</th>
      <td>15090.045</td>
    </tr>
    <tr>
      <th>7</th>
      <td>16447.060</td>
    </tr>
    <tr>
      <th>8</th>
      <td>20657.870</td>
    </tr>
    <tr>
      <th>9</th>
      <td>22441.340</td>
    </tr>
    <tr>
      <th>10</th>
      <td>20882.415</td>
    </tr>
  </tbody>
</table>
</div>



```python
#Построим график изменения расходов по месяцам
costs.pivot_table(
    index='month',  
    columns='channel',  
    values='costs',  
    aggfunc='sum',  
).plot(figsize=(12, 8), grid=True)

plt.title('Расходы по месяцам')
plt.xlabel('Месяц')
plt.ylabel('Расходы')
plt.legend(loc='right', bbox_to_anchor=(1.22, 0.82))
plt.show()
```


    
![png](output_52_0.png)
    



```python
#Построим график изменений по неделям

costs.pivot_table(
    index='week',  
    columns='channel',  
    values='costs',  
    aggfunc='sum',  
).plot(figsize=(12, 8), grid=True)

plt.title('Расходы по неделям')
plt.xlabel('Месяц')
plt.ylabel('Расходы')
plt.legend(loc='right', bbox_to_anchor=(1.22, 0.82))
plt.show()
```


    
![png](output_53_0.png)
    


- По таблице Самые высокие расходы на рекламу видим в сентябре (22тыс), октрябре (21тыс) и августе (20.5тыс). С мая по сентябрь сумма расходов увелечивалась с каждым месяцем. Начальная отметка в мае - 10тыс.


- На графике по месяцам наглядно видим динамику расходов - лидерами являются TipTop и FaceBoom, что мы видели и по табличкам.Пик видим в сентябре.


- На втором графике видим пик расходов на 39 неделе сентября. В июне на 24 и июле на 29 неделе были спад расходов на рекламу.


```python
#Посчитаем САС для каждого источника

cac = (profiles.groupby('channel')
    .agg({'acquisition_cost': 'mean'})
    .rename(columns={'acquisition_cost':'cac'})
    .sort_values('cac', ascending = False)
        
)
cac
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>cac</th>
    </tr>
    <tr>
      <th>channel</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>TipTop</th>
      <td>2.799003</td>
    </tr>
    <tr>
      <th>FaceBoom</th>
      <td>1.113286</td>
    </tr>
    <tr>
      <th>AdNonSense</th>
      <td>1.008054</td>
    </tr>
    <tr>
      <th>lambdaMediaAds</th>
      <td>0.724802</td>
    </tr>
    <tr>
      <th>WahooNetBanner</th>
      <td>0.602245</td>
    </tr>
    <tr>
      <th>RocketSuperAds</th>
      <td>0.412095</td>
    </tr>
    <tr>
      <th>OppleCreativeMedia</th>
      <td>0.250000</td>
    </tr>
    <tr>
      <th>YRabbit</th>
      <td>0.218975</td>
    </tr>
    <tr>
      <th>MediaTornado</th>
      <td>0.218717</td>
    </tr>
    <tr>
      <th>LeapBob</th>
      <td>0.210172</td>
    </tr>
  </tbody>
</table>
</div>



- Самый высокий показатель стоимости привлечения пользователя (САС) у TipTop - 2.8. Это в 2.5 раза выше чем у FaceBoom (1.1). Третья компания по средней стоимости привлечения пользователя AdNonSense - 1.


- Также видим, что мы ничего не тратим на органическую рекламу.

## Оценка окупаемости рекламы.

Используя графики LTV, ROI и CAC, проанализируем окупаемость рекламы. Считаем, что на календаре 1 ноября 2019 года, а в бизнес-плане заложено, что пользователи должны окупаться не позднее чем через две недели после привлечения. Необходимость включения в анализ органических пользователей определим самостоятельно.

- Проанализируем окупаемость рекламы c помощью графиков LTV и ROI, а также графики динамики LTV, CAC и ROI.
- Проверим конверсию пользователей и динамику её изменения. То же самое сделаем с удержанием пользователей. Построим и изучим графики конверсии и удержания.
- Проанализируем окупаемость рекламы с разбивкой по устройствам. Постройте графики LTV и ROI, а также графики динамики LTV, CAC и ROI.
- Проанализируем окупаемость рекламы с разбивкой по странам. Построим графики LTV и ROI, а также графики динамики LTV, CAC и ROI.
- Проанализируем окупаемость рекламы с разбивкой по рекламным каналам. Построим графики LTV и ROI, а также графики динамики LTV, CAC и ROI.
- Ответим на вопросы:

    1) Окупается ли реклама, направленная на привлечение пользователей в целом?
    
    2) Какие устройства, страны и рекламные каналы могут оказывать негативное влияние на окупаемость рекламы?
    
    3) Чем могут быть вызваны проблемы окупаемости?


### Проанализируем окупаемость рекламы.


```python
#Уберем органических пользователей из profiles

profiles = profiles.query('channel != "organic"')

#Зададим момент и горизонт анализа

observation_date = datetime(2019, 11, 1).date()
horizon_days = 14

#Построим графики LTV и ROI

ltv_raw, ltv_grouped, ltv_history, roi_grouped, roi_history = get_ltv(
    profiles, orders, observation_date, horizon_days)

plot_ltv_roi(ltv_grouped, ltv_history, roi_grouped, roi_history, horizon_days) 
```


    
![png](output_59_0.png)
    


На графиках видим:
- LTV растет к концу второй недели и относительно нашего периода всегда нормально распределяется.
- САС стабильно растет начиная с мая, также как и ROI стабильно падает.
- ROI не окупается. ROI находится в районе 0.8 к концу второй недели.
- Реклама окупалась в мае и в середине июня.


### Проанализируем окупаемость рекламы, конверсию и удержание с разбивкой по устройствам


```python
#Построим графики окупаемости с разбивкой по устройствам

ltv_raw, ltv_grouped, ltv_history, roi_grouped, roi_history = get_ltv(
    profiles, orders, observation_date, horizon_days, dimensions = ['device'])

plot_ltv_roi(
    ltv_grouped, ltv_history, roi_grouped, roi_history, horizon_days) 
```


    
![png](output_62_0.png)
    


- LTV растет на всех устройствах, к концу второй недели показатель на MAC, Android и Iphone больше 0.9. На PC чуть больше 0.8. По динамике на 14-й день также наблюдаем стабильное распределение. Кроме PC, тут видим несколько падений LTV в конце июля, начале сентября и середине октября.
- На графике САС мы видим ярко выраженный рост стоимости привлечения пользователей Iphone и Mac. Далее идет Android и PC, на котором почти не увеличивается показатель.
- На графике ROI видим, что на 14-й день окупаются только пользователи на PC. Далее идет Android (0.9), у Mac и Iphone ROI не выше 0.7.
- По месяцам пользователи PC стабильно окупаются, за исключением конца июля и осени. После июля расходы на остальные устройства стабильно не окупаются. Ярче всего падает окупаемость на Iphone и Mac.


```python
#Построим график конверсии

conversion_raw, conversion_grouped, conversion_history = get_conversion(
    profiles, orders, observation_date, horizon_days, dimensions=['device'])

plot_conversion(conversion_grouped, conversion_history, horizon_days)


```


    
![png](output_64_0.png)
    


- Самая низкая конверсия пользователей на PC, самая высокая у Mac.
- Начиная с июня видим много падений конверсии у PC. На Mac и Iphone большое кол-во пиков.


```python
#Построим график удержания

retention_raw, retention_grouped, retention_history = get_retention(
    profiles, visits, observation_date, horizon_days, dimensions=['device'])

plot_retention(retention_grouped, retention_history, horizon_days)
```


    
![png](output_66_0.png)
    


- Удержание платящих пользователей требует более индивидуализированных подходов из-за их прямого вклада в доход компании. Они могут ожидать более высокого уровня сервиса и иметь более высокие требования к продукту. Поэтому компании часто инвестируют больше усилий в удержание платящих пользователей, предлагая персонализированные предложения, высококачественный сервис и программы лояльности. 

- Видим, что у PC более лучший показатель удержания платящих пользователей. 
- В целом на графиках не видим каких-то сильных отклонений или разницы.

### Проанализируем окупаемость рекламы, конверсию и удержание с разбивкой по странам


```python
#Построим графики LTV и ROI
ltv_raw, ltv_grouped, ltv_history, roi_grouped, roi_history = get_ltv(
    profiles, orders, observation_date, horizon_days, dimensions = ['region'])

plot_ltv_roi(
    ltv_grouped, ltv_history, roi_grouped, roi_history, horizon_days)
```


    
![png](output_70_0.png)
    


- Самый стабильный показатель LTV у пользователей из США. Также и в динамике.
- Но мы видим, что у пользователей из США САС (стоимость привлечения) начиная с июня очень резко растет.
- Соответсвенно на графике ROI мы видим, что пользователи из США совершенно не окупаются, в сравнении с остальными странами. Остальные страны окупаются уже на 4-5 день.
- У остальных стран высокий показатель окупаемости, в особенности UK. Окупаемость в июле и августе достигала показателя в 2-3 раза больше уровня окупаемости.


```python
#Построим график конверсии

conversion_raw, conversion_grouped, conversion_history = get_conversion(
    profiles, orders, observation_date, horizon_days, dimensions=['device'])

plot_conversion(conversion_grouped, conversion_history, horizon_days)

```


    
![png](output_72_0.png)
    


- Конверсия самая высокая и стабильная у пользователей из США.


```python
#Построим график удержания

retention_raw, retention_grouped, retention_history = get_retention(
    profiles, visits, observation_date, horizon_days, dimensions=['region'])

plot_retention(retention_grouped, retention_history, horizon_days)
```


    
![png](output_74_0.png)
    


- Пользователи из США имеют стабильно низкий показатель удержания платящих пользователей.

### Проанализируем окупаемость рекламы, конверсию и удержание с разбивкой по рекламным каналам


```python
#Построим графики LTV и ROI

ltv_raw, ltv_grouped, ltv_history, roi_grouped, roi_history = get_ltv(
    profiles, orders, observation_date, horizon_days, dimensions = ['channel'])

plot_ltv_roi(
    ltv_grouped, ltv_history, roi_grouped, roi_history, horizon_days)
```


    
![png](output_77_0.png)
    


- Самый высокий показатель LTV у каналов lambdaMediaAds и TipTop.
- CAC у TipTop также растет быстрее всего. У остальных каналов этот показатель стабильный и не растет.
- Только три канала не окупаются на 14й день - TipTop, FaceBoom и AdNonSense.
- На графике динамики ROI видим, что остальные каналы окупаются и некоторые даже в несколько раз (например, YRabbit, MediaTornado, LeapBob). Вероятно потому, что это компании с самой низкой стоимостью привлечения.


```python
#Построим график конверсии

conversion_raw, conversion_grouped, conversion_history = get_conversion(
    profiles, orders, observation_date, horizon_days, dimensions=['channel'])

plot_conversion(conversion_grouped, conversion_history, horizon_days)
```


    
![png](output_79_0.png)
    


- Самый высокий показатель конверсии у каналов - FaceBoom, AdNonSense,lambdaMediaAds - 0.10 и выше на 14й день.


```python
#Построим график удержания

retention_raw, retention_grouped, retention_history = get_retention(
    profiles, visits, observation_date, horizon_days, dimensions=['channel'])

plot_retention(retention_grouped, retention_history, horizon_days)
```


    
![png](output_81_0.png)
    


- Худший показатель удержания платящих пользователей у каналов FaceBoom и AdNonSense.


# Выводы по исследованию:

1. Загрузили и подготовили данные. Привели названия столбов к нижнему регистру, поменяли тип данных в столбах с датой.
2. Задали функции для расчёта и анализа LTV, ROI, удержания и конверсии
3. Провели иследовательский анализ данных:
- Определили 1 мая 2019 минимальной и 27 октября 2019 максимальной датой исследования.
- Определили, что самый высокий процент платящих пользователей из США -  78%
- Выявили, что платящие пользователи предпочитают iPhone (38%), Android (23%), Mac (22%) и PC (17%).
- Топ-3 каналы привлечения это FaceBoom (40%), TipTop (21%), organic (13%). Все вместе приносят 74% от общего кол-ва.
4. Маркетинг:
- Общая сумма расходов на маркетинг - 105497 UE (так как неизвестна валюта)
- TipTop 54751 UE - это больше 50% от всех расходов и FaceBoom 32445 UE
- Видим в сентябре (22тыс), октрябре (21тыс) и августе (20.5тыс) . Лидеры TipTop и FaceBoom
- САС TipTop 2.8, у FaceBoom 1.1
5. Оценили окупаемость рекламы:
- Проанализировали окупаемость рекламы c помощью графиков LTV и ROI, а также графики динамики LTV, CAC и ROI:
   - По устройствам;
   - По странам;
   - По рекламным компаниям.
   
# Рекомендации:

- LTV растет к концу второй недели, что свидетельствует о потенциале увеличения выручки от пользователей со временем.
- САС стабильно растет начиная с мая, что может быть вызвано увеличением стоимости привлечения новых пользователей. Необходимо оценить эффективность маркетинговых кампаний и возможность снижения затрат.
- Пользователи на PC окупаются лучше других устройств, следует уделить внимание оптимизации кампаний для этой аудитории.
- Рост стоимости привлечения пользователей на Mac и iPhone требует дополнительного анализа и возможной корректировки стратегии.
- Пользователи из США не окупаются, в то время как пользователи из других стран показывают хорошую окупаемость. Рекомендуется пересмотреть стратегию привлечения аудитории из США или скорректировать целевую аудиторию.
- Высокая окупаемость в UK и других странах может быть использована для увеличения прибыли.
- Необходимо пересмотреть стратегию использования каналов, которые не окупаются на 14-й день (TipTop, FaceBoom, AdNonSense).
- Каналы с высоким показателем ROI (YRabbit, MediaTornado, LeapBob) могут быть использованы для увеличения эффективности маркетинговых кампаний.







```python

```
