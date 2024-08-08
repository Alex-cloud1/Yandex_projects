# Анализ A/B-теста интернет-магазина 

# - Описание проекта:
Данный проект представляет собой анализ A/B-теста, проведенного в интернет-магазине с целью увеличения выручки. В рамках проекта будет выполнена приоритизация гипотез по увеличению выручки с использованием фреймворков ICE и RICE. Затем будет проведен анализ результатов A/B-теста с использованием данных о заказах и посетителях.


# - Цель исследования:
Целью данного проекта является выявление эффективности гипотез по увеличению выручки интернет-магазина и анализ результатов A/B-теста для принятия обоснованных решений.


# - Задачи исследования:
- Приоритизация гипотез с помощью фреймворков ICE и RICE.
- Построение графиков кумулятивной выручки, среднего чека, относительного изменения среднего чека, кумулятивного среднего количества заказов на посетителя и относительного изменения этого показателя.
- Анализ данных о заказах и посетителях, включая точечные графики количества заказов на пользователя и стоимости заказов.
- Определение аномальных пользователей и заказов на основе перцентилей.
- Проведение статистического анализа различий в среднем количестве заказов на посетителя и среднем чеке между группами по "сырым" и "очищенным" данным.
- Принятие решения по результатам A/B-теста.


# - План действий:
1. Посмотрим на данные, если потребуется предобработаем.
1. Провести приоритизацию гипотез с использованием фреймворков ICE и RICE.
2. Построить необходимые графики кумулятивной выручки, среднего чека, относительного изменения среднего чека, кумулятивного среднего количества заказов на посетителя и относительного изменения этого показателя.
3. Проанализировать данные о заказах и посетителях, выявить аномалии.
4. Провести статистический анализ различий между группами по ключевым показателям.
5. Принять решение по результатам A/B-теста и подготовить выводы и рекомендации на основе проведенного анализа.


# Предобработка и подготовка данных к анализу


```python
#Загрузим библеотеки

import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
import seaborn as sns
from math import factorial
from scipy.stats import poisson
from scipy.stats import binom
from scipy import stats as st
```


```python
data = pd.read_csv('/datasets/hypothesis.csv')
```


```python
data.info()
pd.options.display.max_colwidth = 130
data.head(10)

```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 9 entries, 0 to 8
    Data columns (total 5 columns):
     #   Column      Non-Null Count  Dtype 
    ---  ------      --------------  ----- 
     0   Hypothesis  9 non-null      object
     1   Reach       9 non-null      int64 
     2   Impact      9 non-null      int64 
     3   Confidence  9 non-null      int64 
     4   Efforts     9 non-null      int64 
    dtypes: int64(4), object(1)
    memory usage: 488.0+ bytes





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
      <th>Hypothesis</th>
      <th>Reach</th>
      <th>Impact</th>
      <th>Confidence</th>
      <th>Efforts</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Добавить два новых канала привлечения трафика, что позволит привлекать на 30% больше пользователей</td>
      <td>3</td>
      <td>10</td>
      <td>8</td>
      <td>6</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Запустить собственную службу доставки, что сократит срок доставки заказов</td>
      <td>2</td>
      <td>5</td>
      <td>4</td>
      <td>10</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Добавить блоки рекомендаций товаров на сайт интернет магазина, чтобы повысить конверсию и средний чек заказа</td>
      <td>8</td>
      <td>3</td>
      <td>7</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Изменить структура категорий, что увеличит конверсию, т.к. пользователи быстрее найдут нужный товар</td>
      <td>8</td>
      <td>3</td>
      <td>3</td>
      <td>8</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Изменить цвет фона главной страницы, чтобы увеличить вовлеченность пользователей</td>
      <td>3</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Добавить страницу отзывов клиентов о магазине, что позволит увеличить количество заказов</td>
      <td>3</td>
      <td>2</td>
      <td>2</td>
      <td>3</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Показать на главной странице баннеры с актуальными акциями и распродажами, чтобы увеличить конверсию</td>
      <td>5</td>
      <td>3</td>
      <td>8</td>
      <td>3</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Добавить форму подписки на все основные страницы, чтобы собрать базу клиентов для email-рассылок</td>
      <td>10</td>
      <td>7</td>
      <td>8</td>
      <td>5</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Запустить акцию, дающую скидку на товар в день рождения</td>
      <td>1</td>
      <td>9</td>
      <td>9</td>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>



Тип данных верный, пропусков в данных нет. Нужно привести названия столбцов к нижнему регистру для удобного использования.


```python
#Приведем названия к нижнему регистру
data.columns = data.columns.str.lower()
data.head()
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
      <th>hypothesis</th>
      <th>reach</th>
      <th>impact</th>
      <th>confidence</th>
      <th>efforts</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Добавить два новых канала привлечения трафика, что позволит привлекать на 30% больше пользователей</td>
      <td>3</td>
      <td>10</td>
      <td>8</td>
      <td>6</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Запустить собственную службу доставки, что сократит срок доставки заказов</td>
      <td>2</td>
      <td>5</td>
      <td>4</td>
      <td>10</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Добавить блоки рекомендаций товаров на сайт интернет магазина, чтобы повысить конверсию и средний чек заказа</td>
      <td>8</td>
      <td>3</td>
      <td>7</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Изменить структура категорий, что увеличит конверсию, т.к. пользователи быстрее найдут нужный товар</td>
      <td>8</td>
      <td>3</td>
      <td>3</td>
      <td>8</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Изменить цвет фона главной страницы, чтобы увеличить вовлеченность пользователей</td>
      <td>3</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Посмотрим на дубликаты
#data.isna().sum()
data.duplicated().sum()
```




    0



Дубликатов не обнаружили

# Часть 1. Приоритизация гипотез.


```python
#Применим ICE для приоритизации гипотез

data['ICE'] = data['impact'] * data['confidence'] / data['efforts']
data['ICE'] = data['ICE'].round(2)
display (data)
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
      <th>hypothesis</th>
      <th>reach</th>
      <th>impact</th>
      <th>confidence</th>
      <th>efforts</th>
      <th>ICE</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Добавить два новых канала привлечения трафика, что позволит привлекать на 30% больше пользователей</td>
      <td>3</td>
      <td>10</td>
      <td>8</td>
      <td>6</td>
      <td>13.33</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Запустить собственную службу доставки, что сократит срок доставки заказов</td>
      <td>2</td>
      <td>5</td>
      <td>4</td>
      <td>10</td>
      <td>2.00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Добавить блоки рекомендаций товаров на сайт интернет магазина, чтобы повысить конверсию и средний чек заказа</td>
      <td>8</td>
      <td>3</td>
      <td>7</td>
      <td>3</td>
      <td>7.00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Изменить структура категорий, что увеличит конверсию, т.к. пользователи быстрее найдут нужный товар</td>
      <td>8</td>
      <td>3</td>
      <td>3</td>
      <td>8</td>
      <td>1.12</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Изменить цвет фона главной страницы, чтобы увеличить вовлеченность пользователей</td>
      <td>3</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1.00</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Добавить страницу отзывов клиентов о магазине, что позволит увеличить количество заказов</td>
      <td>3</td>
      <td>2</td>
      <td>2</td>
      <td>3</td>
      <td>1.33</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Показать на главной странице баннеры с актуальными акциями и распродажами, чтобы увеличить конверсию</td>
      <td>5</td>
      <td>3</td>
      <td>8</td>
      <td>3</td>
      <td>8.00</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Добавить форму подписки на все основные страницы, чтобы собрать базу клиентов для email-рассылок</td>
      <td>10</td>
      <td>7</td>
      <td>8</td>
      <td>5</td>
      <td>11.20</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Запустить акцию, дающую скидку на товар в день рождения</td>
      <td>1</td>
      <td>9</td>
      <td>9</td>
      <td>5</td>
      <td>16.20</td>
    </tr>
  </tbody>
</table>
</div>



```python
#Применим RICE для приоритизации гипотез

data['RICE'] = data['reach'] * data['impact'] * data['confidence'] / data['efforts']
display (data)
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
      <th>hypothesis</th>
      <th>reach</th>
      <th>impact</th>
      <th>confidence</th>
      <th>efforts</th>
      <th>ICE</th>
      <th>RICE</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Добавить два новых канала привлечения трафика, что позволит привлекать на 30% больше пользователей</td>
      <td>3</td>
      <td>10</td>
      <td>8</td>
      <td>6</td>
      <td>13.33</td>
      <td>40.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Запустить собственную службу доставки, что сократит срок доставки заказов</td>
      <td>2</td>
      <td>5</td>
      <td>4</td>
      <td>10</td>
      <td>2.00</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Добавить блоки рекомендаций товаров на сайт интернет магазина, чтобы повысить конверсию и средний чек заказа</td>
      <td>8</td>
      <td>3</td>
      <td>7</td>
      <td>3</td>
      <td>7.00</td>
      <td>56.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Изменить структура категорий, что увеличит конверсию, т.к. пользователи быстрее найдут нужный товар</td>
      <td>8</td>
      <td>3</td>
      <td>3</td>
      <td>8</td>
      <td>1.12</td>
      <td>9.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Изменить цвет фона главной страницы, чтобы увеличить вовлеченность пользователей</td>
      <td>3</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1.00</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Добавить страницу отзывов клиентов о магазине, что позволит увеличить количество заказов</td>
      <td>3</td>
      <td>2</td>
      <td>2</td>
      <td>3</td>
      <td>1.33</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Показать на главной странице баннеры с актуальными акциями и распродажами, чтобы увеличить конверсию</td>
      <td>5</td>
      <td>3</td>
      <td>8</td>
      <td>3</td>
      <td>8.00</td>
      <td>40.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Добавить форму подписки на все основные страницы, чтобы собрать базу клиентов для email-рассылок</td>
      <td>10</td>
      <td>7</td>
      <td>8</td>
      <td>5</td>
      <td>11.20</td>
      <td>112.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Запустить акцию, дающую скидку на товар в день рождения</td>
      <td>1</td>
      <td>9</td>
      <td>9</td>
      <td>5</td>
      <td>16.20</td>
      <td>16.2</td>
    </tr>
  </tbody>
</table>
</div>


- При использовании фреймворка RICE приоритет гипотез изменяется по сравнению с ICE из-за учета параметра Reach (охват). Гипотезы, которые имеют большой охват:
  -  гипотеза 7 (Добавить форму подписки на все основные страницы, чтобы собрать базу клиентов для email-рассылок)   
  -  гипотеза 2 (Добавить блоки рекомендаций товаров на сайт интернет магазина, чтобы повысить конверсию и средний чек заказа));
  
  - гипотеза 3 (Изменить структура категорий, что увеличит конверсию, т.к. пользователи быстрее найдут нужный товар).


- Гипотезы с высоким охватом и высоким влиянием на пользователей могут получить более высокий приоритет по RICE, даже если у них невысокая уверенность или высокие усилия.
- RICE учитывает не только потенциальную значимость гипотезы, но и ее реализуемость и охват аудитории, что может привести к изменению порядка приоритетности гипотез по сравнению с ICE.


# Часть 2. Анализ A/B-теста

Мы провели A/B-тест и получили результаты, которые описаны в файлах /datasets/orders.csv и /datasets/visitors.csv. Посмотрим на данные, которые хранятся в этим таблицах.



# Предобработка данных после проведенного A/B теста.


```python
orders1 = pd.read_csv('/datasets/orders.csv')
visitors = pd.read_csv('/datasets/visitors.csv')
```


```python
#Посмотрим датафрейм orders

orders1.info()
orders1.head()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 1197 entries, 0 to 1196
    Data columns (total 5 columns):
     #   Column         Non-Null Count  Dtype 
    ---  ------         --------------  ----- 
     0   transactionId  1197 non-null   int64 
     1   visitorId      1197 non-null   int64 
     2   date           1197 non-null   object
     3   revenue        1197 non-null   int64 
     4   group          1197 non-null   object
    dtypes: int64(3), object(2)
    memory usage: 46.9+ KB





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
      <th>transactionId</th>
      <th>visitorId</th>
      <th>date</th>
      <th>revenue</th>
      <th>group</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>3667963787</td>
      <td>3312258926</td>
      <td>2019-08-15</td>
      <td>1650</td>
      <td>B</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2804400009</td>
      <td>3642806036</td>
      <td>2019-08-15</td>
      <td>730</td>
      <td>B</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2961555356</td>
      <td>4069496402</td>
      <td>2019-08-15</td>
      <td>400</td>
      <td>A</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3797467345</td>
      <td>1196621759</td>
      <td>2019-08-15</td>
      <td>9759</td>
      <td>B</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2282983706</td>
      <td>2322279887</td>
      <td>2019-08-15</td>
      <td>2308</td>
      <td>B</td>
    </tr>
  </tbody>
</table>
</div>



Пропусков данных нет. В столбце date нужно привести данные в формат datetime. Также привести в первых двух столбцах названия к нижнему регистру.



```python
#Поменяем тип данных и приведем названия к нижнему регистру

orders1['date'] = pd.to_datetime(orders1['date'])
orders1.columns = orders1.columns.str.lower()

#Проверим данные

orders1.info()
orders1.head()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 1197 entries, 0 to 1196
    Data columns (total 5 columns):
     #   Column         Non-Null Count  Dtype         
    ---  ------         --------------  -----         
     0   transactionid  1197 non-null   int64         
     1   visitorid      1197 non-null   int64         
     2   date           1197 non-null   datetime64[ns]
     3   revenue        1197 non-null   int64         
     4   group          1197 non-null   object        
    dtypes: datetime64[ns](1), int64(3), object(1)
    memory usage: 46.9+ KB





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
      <th>transactionid</th>
      <th>visitorid</th>
      <th>date</th>
      <th>revenue</th>
      <th>group</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>3667963787</td>
      <td>3312258926</td>
      <td>2019-08-15</td>
      <td>1650</td>
      <td>B</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2804400009</td>
      <td>3642806036</td>
      <td>2019-08-15</td>
      <td>730</td>
      <td>B</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2961555356</td>
      <td>4069496402</td>
      <td>2019-08-15</td>
      <td>400</td>
      <td>A</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3797467345</td>
      <td>1196621759</td>
      <td>2019-08-15</td>
      <td>9759</td>
      <td>B</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2282983706</td>
      <td>2322279887</td>
      <td>2019-08-15</td>
      <td>2308</td>
      <td>B</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Посмотрим датафрейм visitors

visitors.info()
visitors.head()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 62 entries, 0 to 61
    Data columns (total 3 columns):
     #   Column    Non-Null Count  Dtype 
    ---  ------    --------------  ----- 
     0   date      62 non-null     object
     1   group     62 non-null     object
     2   visitors  62 non-null     int64 
    dtypes: int64(1), object(2)
    memory usage: 1.6+ KB





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
      <th>date</th>
      <th>group</th>
      <th>visitors</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2019-08-01</td>
      <td>A</td>
      <td>719</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2019-08-02</td>
      <td>A</td>
      <td>619</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2019-08-03</td>
      <td>A</td>
      <td>507</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2019-08-04</td>
      <td>A</td>
      <td>717</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2019-08-05</td>
      <td>A</td>
      <td>756</td>
    </tr>
  </tbody>
</table>
</div>



Пропусков не выявлено. Нужно поменять формат данных в столбце date.


```python
#Поменяем тип данных.

visitors['date'] = pd.to_datetime(visitors['date'])
visitors.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 62 entries, 0 to 61
    Data columns (total 3 columns):
     #   Column    Non-Null Count  Dtype         
    ---  ------    --------------  -----         
     0   date      62 non-null     datetime64[ns]
     1   group     62 non-null     object        
     2   visitors  62 non-null     int64         
    dtypes: datetime64[ns](1), int64(1), object(1)
    memory usage: 1.6+ KB



```python
#Посмотрим на дубликаты в обоих таблицах

display (orders1.duplicated().sum())
display (visitors.duplicated().sum())
```


    0



    0



```python
orders1[orders1.duplicated(subset=['visitorid'], keep=False)].sort_values(by='visitorid')
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
      <th>transactionid</th>
      <th>visitorid</th>
      <th>date</th>
      <th>revenue</th>
      <th>group</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>71</th>
      <td>3679129301</td>
      <td>8300375</td>
      <td>2019-08-01</td>
      <td>10510</td>
      <td>B</td>
    </tr>
    <tr>
      <th>703</th>
      <td>4293855558</td>
      <td>8300375</td>
      <td>2019-08-07</td>
      <td>1790</td>
      <td>A</td>
    </tr>
    <tr>
      <th>1102</th>
      <td>1941707234</td>
      <td>199603092</td>
      <td>2019-08-12</td>
      <td>6438</td>
      <td>A</td>
    </tr>
    <tr>
      <th>823</th>
      <td>2971973105</td>
      <td>199603092</td>
      <td>2019-08-27</td>
      <td>2790</td>
      <td>A</td>
    </tr>
    <tr>
      <th>134</th>
      <td>62794304</td>
      <td>199603092</td>
      <td>2019-08-22</td>
      <td>6976</td>
      <td>A</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1019</th>
      <td>453824282</td>
      <td>4256040402</td>
      <td>2019-08-31</td>
      <td>1850</td>
      <td>B</td>
    </tr>
    <tr>
      <th>253</th>
      <td>1610091760</td>
      <td>4256040402</td>
      <td>2019-08-23</td>
      <td>16950</td>
      <td>A</td>
    </tr>
    <tr>
      <th>263</th>
      <td>2575861846</td>
      <td>4256040402</td>
      <td>2019-08-18</td>
      <td>10970</td>
      <td>B</td>
    </tr>
    <tr>
      <th>682</th>
      <td>1216533772</td>
      <td>4266935830</td>
      <td>2019-08-29</td>
      <td>78990</td>
      <td>B</td>
    </tr>
    <tr>
      <th>662</th>
      <td>1811671147</td>
      <td>4266935830</td>
      <td>2019-08-29</td>
      <td>78990</td>
      <td>A</td>
    </tr>
  </tbody>
</table>
<p>260 rows × 5 columns</p>
</div>



Обнаружили 260 строк дубликатов. Посмотрим есть ли пользователи, которые попали в обе группы теста.



```python
#Создаем функцию, чтобы проверить пользователей на уникальность

def uniq(data):
    usersA = data.query('group == "A"')['visitorid']
    usersB= data.query('group == "B"')['visitorid']
    same_users = usersB[usersB.isin(usersA)]
    display(same_users.nunique())
    return(same_users)
```


```python
same_visitors = uniq(orders1)
```


    58


- Нашли 58 пользователей - дубликатов. Избавимся от этих чисел чтобы не путать данные.


```python
orders = orders1.query('visitorid not in @same_visitors')

#Проверим руезультат

uniq(orders)
```


    0





    Series([], Name: visitorid, dtype: int64)



- Избалились от дубликатов

## График кумулятивной выручки по группам.


```python
#Создадим датафрейм datesGroups с уникальными парами значений date и group, таблицы orders и избавимся от дубликатов

dateGroups = orders[['date', 'group']].drop_duplicates()

#Объявим переменную ordersAggregated и visitorsAggregated

ordersAggregated = dateGroups.apply(
    lambda x: orders[np.logical_and(orders['date'] <= x['date'], orders['group'] == x['group'])].agg({
        'date': 'max',
        'group': 'max',
        'transactionid': 'nunique',
        'visitorid': 'nunique',
        'revenue': 'sum'
    }), axis=1).sort_values(by=['date', 'group'])


visitorsAggregated = dateGroups.apply(
    lambda x: visitors[np.logical_and(visitors['date'] <= x['date'], visitors['group'] == x['group'])].agg({
        'date' : 'max',
        'group' : 'max', 
        'visitors' : 'sum'
    }), axis=1).sort_values(by=['date','group'])



#Обединим ordersAggregated и visitorsAggregated в переменную cumulativeData. Далее добавим столбец 'conversion'

cumulativeData = ordersAggregated.merge(visitorsAggregated, left_on=['date', 'group'], right_on=['date', 'group'])
cumulativeData.columns = ['date', 'group', 'orders', 'buyers', 'revenue', 'visitors']

cumulativeData['conversion'] = (cumulativeData['orders'] / cumulativeData['visitors'])
display (cumulativeData.head())

#Теперь создадим переменные cumulativeDataA и cumulativeDataB , в которых будут результаты по группам A и B

cumulativeDataA = cumulativeData[cumulativeData['group']=='A'][['date','revenue', 'orders','visitors',]]
cumulativeDataB = cumulativeData[cumulativeData['group']=='B'][['date','revenue', 'orders', 'visitors']]




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
      <th>date</th>
      <th>group</th>
      <th>orders</th>
      <th>buyers</th>
      <th>revenue</th>
      <th>visitors</th>
      <th>conversion</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2019-08-01</td>
      <td>A</td>
      <td>23</td>
      <td>19</td>
      <td>142779</td>
      <td>719</td>
      <td>0.031989</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2019-08-01</td>
      <td>B</td>
      <td>17</td>
      <td>17</td>
      <td>59758</td>
      <td>713</td>
      <td>0.023843</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2019-08-02</td>
      <td>A</td>
      <td>42</td>
      <td>36</td>
      <td>234381</td>
      <td>1338</td>
      <td>0.031390</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2019-08-02</td>
      <td>B</td>
      <td>40</td>
      <td>39</td>
      <td>221801</td>
      <td>1294</td>
      <td>0.030912</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2019-08-03</td>
      <td>A</td>
      <td>66</td>
      <td>60</td>
      <td>346854</td>
      <td>1845</td>
      <td>0.035772</td>
    </tr>
  </tbody>
</table>
</div>



```python
cumulativeData['date'].min()
cumulativeData['date'].max()
```




    Timestamp('2019-08-31 00:00:00')



-  Минимальная и максимальная даты совпадают с исходными данными


```python
#Теперь построим график выручки по группам.

plt.figure(figsize=(12, 6))
plt.plot(cumulativeDataA['date'], cumulativeDataA['revenue'], label='Группа A')
plt.plot(cumulativeDataB['date'], cumulativeDataB['revenue'], label='Группа B')
plt.ylabel('Кумулятивная выручка')
plt.title('Кумулятивная выручка по группам A и B')
plt.legend()
plt.grid(True)
plt.xticks(rotation=45)
plt.show()


```


    
![png](output_36_0.png)
    


- В первые 5 дней выручка в обоих группах растет одинаково. Далее, выручка группы B начинает более явно расти и до конца месяца лидирует. Был явный скачок в районе 18 августа, скорее всего это связано с дорогим или дорогими заказами в этот день. Группа B 13 августа сравнивается по показателю выручки с группой A, но далее растет очень умеренно в сравнении с группой B.
- Такую причину скачка можно описать несколькими возможными причинами. Например, мог вырасти средний чек заказов или их количество. Посмотрим на результаты дальше.

## График кумулятивного среднего чека по группам.


```python
#Посчитаем средний кумулятивный чек по группам — разделим кумулятивную выручку на кумулятивное число заказов
#Посмотрим график

plt.figure(figsize=(12, 6))
plt.plot(cumulativeDataA['date'], cumulativeDataA['revenue']/cumulativeDataA['orders'], label='Группа A')
plt.plot(cumulativeDataB['date'], cumulativeDataB['revenue']/cumulativeDataB['orders'], label='Группа B')
plt.ylabel('Средний чек')
plt.title('Кумулятивный средний чек по группам A и B')
plt.legend()
plt.grid(True)
plt.xticks(rotation=45)
plt.show()

```


    
![png](output_39_0.png)
    


- На графике среднего чека видим следующее: группа B преимущественно лидирует на этом графике, за исключением периода с 11 по 15 августа; далее видим всплеск среднего чека 18 августа в группе B и небольшое падение к концу месяца; средний чек группы A после всплеска 13 августа также умеренно пдаает к концу месяца.

## График относительного изменения кумулятивного среднего чека группы B к группе A.


```python
#Объединим таблицы cumulativeDataA и cumulativeDataB  в переменную mergedCumulativeData

mergedCumulativeData = cumulativeDataA.merge(
    cumulativeDataB,
    left_on='date',
    right_on='date',
    how='left',
    suffixes=['A', 'B']
)

#Построим график относительного изменения кумулятивного среднего чека группы B к группе A

plt.figure(figsize=(12,6))
plt.plot(mergedCumulativeData['date'], (
    mergedCumulativeData['revenueB']/mergedCumulativeData['ordersB'])/
    (mergedCumulativeData['revenueA']/mergedCumulativeData['ordersA'])-1)
plt.title('Относительное изменение кумулятивного среднего чека групп "B" к "А"')
plt.grid(True)
plt.axhline(y=0, color='black', linestyle='--') ;
plt.xticks(rotation=45)
plt.show()



```


    
![png](output_42_0.png)
    


- Средний чек группы B рос начиная с 1 августа, 2 августа уже перерос группу A. 8 августа показатель начал снижаться и 11 числа группа A вышла вперед до 15 числа. Далее, был рост группы B - 15 августа вышли вперед, 18 август был выше описанный скачок и вплоть до конца опережали группу A. 

## График кумулятивного среднего количества заказов на посетителя по группам.


```python
#Построим график кумулятивного среднего количества заказов на посетителя по группам

plt.figure(figsize=(12, 6))
plt.plot(mergedCumulativeData['date'], mergedCumulativeData['ordersA']/mergedCumulativeData['visitorsA'], label='Группа A')
plt.plot(mergedCumulativeData['date'], mergedCumulativeData['ordersB']/mergedCumulativeData['visitorsB'], label='Группа B')
plt.ylabel('Среднее кол-во заказов')
plt.title('Кумулятивное среднее кол-во заказов по группам A и B')
plt.legend()
plt.grid(True)
plt.xticks(rotation=45)
plt.show()
```


    
![png](output_45_0.png)
    


- В группе А с первого дня был более высокий уровень заказов (0.033), 2 числа был резкий скачок и далее падение до 4 августа ; после 5 августа показатель пошел на спад и после 9 августа кол-во заказов не превышало 0.031;
- В группе B был скачок 2 августа, далее падение и 4 августа снова прирост заказов. Также, были резкие всплески с 7 по 10 августа и с 14 по 17 августа. Далее график идет на умеренный спад. После 7 августа показатель не падал ниже 0.033 заказов в день

## График относительного изменения кумулятивного среднего количества заказов на посетителя группы B к группе A


```python
#Построим график относительного изменения 

plt.figure(figsize=(12,6))
plt.plot(mergedCumulativeData['date'], (
    mergedCumulativeData['ordersB']/mergedCumulativeData['visitorsB'])/
    (mergedCumulativeData['ordersA']/mergedCumulativeData['visitorsA'])-1)
plt.title('Относительное изменение кумулятивного среднего чека групп "B" к "А"')
plt.grid(True)
plt.axhline(y=0, color='black', linestyle='--') ;
plt.xticks(rotation=45)
plt.show()

```


    
![png](output_48_0.png)
    


- 2 августа кумулятивный средний чек был выше у группы B; далее, до 5 числа лидировала группа A; с 5 августа до конца месяца постоянно лидировала группа B.

## Точечный график количества заказов по пользователям.


```python
#Создадим переменную с количеством заказов по пользователям

ordersByUsers = orders.groupby(
    'visitorid', as_index=False).agg(
    {'transactionid':'nunique',
    'group':'max',
    'date':'max'}).rename(
    columns={'transactionid':'orders'})
ordersByUsers.columns = ['visitorid','orders','group', 'date']

ordersByUsers.sort_values(by = 'orders', ascending = False).head(10)
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
      <th>visitorid</th>
      <th>orders</th>
      <th>group</th>
      <th>date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>908</th>
      <td>3967698036</td>
      <td>3</td>
      <td>A</td>
      <td>2019-08-02</td>
    </tr>
    <tr>
      <th>55</th>
      <td>249864742</td>
      <td>3</td>
      <td>B</td>
      <td>2019-08-24</td>
    </tr>
    <tr>
      <th>478</th>
      <td>2108163459</td>
      <td>3</td>
      <td>A</td>
      <td>2019-08-09</td>
    </tr>
    <tr>
      <th>687</th>
      <td>2988190573</td>
      <td>3</td>
      <td>A</td>
      <td>2019-08-30</td>
    </tr>
    <tr>
      <th>890</th>
      <td>3908431265</td>
      <td>3</td>
      <td>A</td>
      <td>2019-08-06</td>
    </tr>
    <tr>
      <th>138</th>
      <td>611059232</td>
      <td>3</td>
      <td>A</td>
      <td>2019-08-01</td>
    </tr>
    <tr>
      <th>632</th>
      <td>2742574263</td>
      <td>3</td>
      <td>B</td>
      <td>2019-08-22</td>
    </tr>
    <tr>
      <th>157</th>
      <td>678354126</td>
      <td>2</td>
      <td>A</td>
      <td>2019-08-28</td>
    </tr>
    <tr>
      <th>323</th>
      <td>1404560065</td>
      <td>2</td>
      <td>B</td>
      <td>2019-08-27</td>
    </tr>
    <tr>
      <th>452</th>
      <td>1985475298</td>
      <td>2</td>
      <td>B</td>
      <td>2019-08-13</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Построим точечный график

x_values = pd.Series(range(0,len(ordersByUsers['orders'])))

plt.figure(figsize=(10,5))
plt.scatter(x_values, ordersByUsers['orders'], color='navy')
plt.title('Количество заказов по пользователям')
plt.show()
```


    
![png](output_52_0.png)
    


- Только 7 пользователей совершали 3 покупки, в основном у пользователей 1-2 покупки. 

## 95-й и 99-й перцентили количества заказов на пользователя.


```python
np.percentile(ordersByUsers['orders'], [95, 99])
```




    array([1., 2.])



- 95 перцентиль - 1 заказ
- 99 перцентиль - 2 заказа
- 1-2 заказа - нормальные границы заказов.

## Точечный график стоимостей заказов


```python
x_values = pd.Series(range(0,len(orders['revenue'])))

plt.figure(figsize=(10,5))
plt.scatter(x_values, orders['revenue'] , color='navy')
plt.title('Стоимость заказов')
plt.show()

print (orders.sort_values('revenue', ascending = False).head(10))
```


    
![png](output_58_0.png)
    


          transactionid   visitorid       date  revenue group
    425       590470918  1920142716 2019-08-19  1294500     B
    1196     3936777065  2108080724 2019-08-15   202740     B
    1136      666610489  1307669133 2019-08-13    92550     A
    744      3668308183   888512513 2019-08-27    86620     B
    743      3603576309  4133034833 2019-08-09    67990     A
    1103     1348774318  1164614297 2019-08-12    66350     A
    1099      316924019   148427295 2019-08-12    65710     A
    949      1347999392   887908475 2019-08-21    60450     A
    940      2420050534  4003628586 2019-08-08    58550     B
    131      3163614039  2254586615 2019-08-22    53904     A



```python
filtered_revenue = orders['revenue'].clip(upper=100000)

plt.figure(figsize=(10, 5))
plt.scatter(x_values, filtered_revenue, color='navy')
plt.title('Стоимость заказов (ограниченная до 100000)')
plt.show()

```


    
![png](output_59_0.png)
    


- Большая часть заказов лежит в пределах 20000 уе 

- На графике видим 2 выброса (1.294.500 уе и 202.740 уе) ; один из них наверняка и есть тот самый скачок 18 августа на графиках выручки и средней стоимости заказов. Остальные заказы не превышают 92 тыс.уе

## 95-й и 99-й перцентили стоимости заказов.


```python
np.percentile(orders['revenue'], [95, 99])
```




    array([26785., 53904.])



- 95 перцентиль - 26785 уе
- 99 перцентиль - 53904 уе
- 99% заказов ниже 53904 уе, возьмем это число в качестве границы для аномалий

## Статистическая значимость различий в среднем количестве заказов на посетителя между группами по «сырым» данным.

Для начала исследования по статистистической значимости, зададим нулевую и альтернативную гипотезу.
- H0 - статистически значимых различий в среднем количестве заказов на посетителя между группами А и В по «сырым» данным нет.
- H1 - статистически значимые различия в среднем количестве заказов на посетителя между группами А и В по «сырым» данным есть.


```python
#Создадим переменные для групп и посчитаем количество заказов.
#Сначала объединим данные в общую таблицу

ordersCumulativeData = orders.merge(
    cumulativeData, left_on=['date', 'group'], right_on=['date', 'group'])

ordersCumulativeData = ordersCumulativeData.rename(columns={
                              'transactionid':'transactionid', 
                              'visitorid': 'visitorid', 
                              'date': 'date',
                              'revenue_x': 'revenue_per_user',
                              'group': 'group',
                              'orders': 'orders_daily',
                              'buyers': 'buyers_daily',
                              'revenue_y': 'revenue_daily',
                              'visitors':'visitors_daily'})
ordersCumulativeData.head()
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
      <th>transactionid</th>
      <th>visitorid</th>
      <th>date</th>
      <th>revenue_per_user</th>
      <th>group</th>
      <th>orders_daily</th>
      <th>buyers_daily</th>
      <th>revenue_daily</th>
      <th>visitors_daily</th>
      <th>conversion</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>3667963787</td>
      <td>3312258926</td>
      <td>2019-08-15</td>
      <td>1650</td>
      <td>B</td>
      <td>289</td>
      <td>284</td>
      <td>1960427</td>
      <td>9600</td>
      <td>0.030104</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2804400009</td>
      <td>3642806036</td>
      <td>2019-08-15</td>
      <td>730</td>
      <td>B</td>
      <td>289</td>
      <td>284</td>
      <td>1960427</td>
      <td>9600</td>
      <td>0.030104</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3797467345</td>
      <td>1196621759</td>
      <td>2019-08-15</td>
      <td>9759</td>
      <td>B</td>
      <td>289</td>
      <td>284</td>
      <td>1960427</td>
      <td>9600</td>
      <td>0.030104</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2282983706</td>
      <td>2322279887</td>
      <td>2019-08-15</td>
      <td>2308</td>
      <td>B</td>
      <td>289</td>
      <td>284</td>
      <td>1960427</td>
      <td>9600</td>
      <td>0.030104</td>
    </tr>
    <tr>
      <th>4</th>
      <td>182168103</td>
      <td>935554773</td>
      <td>2019-08-15</td>
      <td>2210</td>
      <td>B</td>
      <td>289</td>
      <td>284</td>
      <td>1960427</td>
      <td>9600</td>
      <td>0.030104</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Создаем переменные для подсчета заказов в разных группах

ordersA = (
    ordersCumulativeData[ordersCumulativeData['group'] == 'A'][['date', 'transactionid', 'visitorid']]
    .groupby('date', as_index=False)
    .agg({'transactionid': pd.Series.nunique})
)
ordersA.columns = ['date', 'orders_a']


ordersB = (
    ordersCumulativeData[ordersCumulativeData['group'] == 'B'][['date', 'transactionid', 'visitorid']]
    .groupby('date', as_index=False)
    .agg({'transactionid': pd.Series.nunique})
)
ordersB.columns = ['date', 'orders_b']

dataOrders = (
    ordersA.merge(
        ordersB, left_on='date', right_on='date', how='left'
    ))
```


```python
# Зададим переменные sampleA и sampleB, в которых пользователям из разных групп будет соответствовать количество заказов

sampleA = pd.concat([ordersA['orders_a'], 
                           pd.Series(0, index=np.arange(dataOrders['orders_a'].sum() - len(ordersA['orders_a'])), 
                                     name='orders',),],axis=0,)

sampleB = pd.concat([ordersB['orders_b'], 
                           pd.Series(0, index=np.arange(dataOrders['orders_b'].sum() - len(ordersB['orders_b'])),
                                     name='orders',),],axis=0,)
display('p-value: ' + "{0:.3f}".format(st.mannwhitneyu(dataOrders['orders_a'], dataOrders['orders_b'])[1]))
display('Относительный : ' + "{0:.3f}".format(dataOrders['orders_b'].mean() / dataOrders['orders_a'].mean() - 1))
```


    'p-value: 0.049'



    'Относительный : 0.171'


- p-value - 4.9% и это значит, что различия в среднем количестве заказов на посетителя можно считать статистически значимыми.
- Относительный прирост среднего кол-ва заказов группы B к группе A 17%

##  Статистическая значимость различий в среднем чеке заказа между группами по «сырым» данным.

- H0 - статистически значимых различий в среднем чеке заказа между группами A и B нет
- H1 - средние чеки заказа между группами A и B статистически различаются


```python
#Создаем переменные для подсчета стат значимости различий по среднему чеку

revenueA = (
    ordersCumulativeData[ordersCumulativeData['group'] == 'A'][['date', 'revenue_per_user']]
    .groupby('date', as_index=False)
    .agg({'revenue_per_user': 'sum'})
)
revenueA.columns = ['date', 'revenue_a']


revenueB = (
    ordersCumulativeData[ordersCumulativeData['group'] == 'B'][['date', 'revenue_per_user']]
    .groupby('date', as_index=False)
    .agg({'revenue_per_user': 'sum'})
)
revenueB.columns = ['date', 'revenue_b']

dataRevenue = (
    revenueA.merge(
        revenueB, left_on='date', right_on='date', how='left'
    ))
```


```python
sampleRevA = pd.concat([revenueA['revenue_a'], 
                           pd.Series(0, index=np.arange(dataRevenue['revenue_a'].sum() - len(revenueA['revenue_a'])), 
                                     name='orders',),],axis=0,)

sampleRevB = pd.concat([revenueB['revenue_b'], 
                           pd.Series(0, index=np.arange(dataRevenue['revenue_b'].sum() - len(revenueB['revenue_b'])),
                                     name='orders',),],axis=0,)

display('p-value: ' + "{0:.3f}".format(st.mannwhitneyu(dataRevenue['revenue_a'], dataRevenue['revenue_b'])[1]))
display('Относительный прирост: ' +  "{0:.3f}".format(dataRevenue['revenue_b'].mean() / dataRevenue['revenue_a'].mean() - 1))
```


    'p-value: 0.226'



    'Относительный прирост: 0.507'


- P-value - 22.6% и это означает, что статистически значимых различий среднего чека заказов между группами нет.
- Относительный прирост среднего чека группы B к группе A составляет 50%. 

## Статистическая значимость различий в среднем количестве заказов на посетителя между группами по «очищенным» данным.

- H0 - статистически значимых различий в среднем количестве заказов на посетителя между группами A и B нет
- H1 - среднее количество заказов на посетителя между группами A и B статистически различается


```python
#Отфильтруем данные и создадим переменные по очищенным данным

ordersPerUsers = ordersCumulativeData.query(
    'revenue_per_user < 54000 and visitorid in @ordersCumulativeData["visitorid"]')

ordersAfiltered = (    
    ordersPerUsers[ordersPerUsers['group'] == 'A'][['date', 'transactionid', 'visitorid']]
    .groupby('date', as_index=False)
    .agg({'transactionid': pd.Series.nunique}))

ordersAfiltered.columns = ['date', 'orders_a']

ordersBfiltered = (
    ordersPerUsers[ordersPerUsers['group'] == 'B'][['date', 'transactionid', 'visitorid']]
    .groupby('date', as_index=False)
    .agg({'transactionid': pd.Series.nunique})
)
ordersBfiltered.columns = ['date', 'orders_b']

dataOrdersFiltered = (
    ordersAfiltered.merge(
        ordersBfiltered, left_on='date', right_on='date', how='left'
    ))
```


```python
#Выведем p-value и относительный прирост группы B относительно группы A

display('p-value: ' +  "{0:.3f}".format(st.mannwhitneyu(
    dataOrdersFiltered['orders_a'], dataOrdersFiltered['orders_b'])[1]))

display('Относительный прирост: ' +  "{0:.3f}".format(
    dataOrdersFiltered['orders_b'].mean() / dataOrdersFiltered['orders_a'].mean() - 1))
```


    'p-value: 0.044'



    'Относительный прирост: 0.175'


- P-value - 4.4%. Это указывает на наличие статистически значимых различий между группами в среднем количестве заказов на посетителя по «очищенным» данным.
- Относительный прирост - 17.5%. Это говорит о том, что в группе B в среднем на 17.5% больше заказов по сравнению с группой A.

## Статистическая значимость различий в среднем чеке заказа между группами по «очищенным» данным.

- H0 - статистически значимых различий в среднем чеке заказа  между группами A и B нет
- H1 - средний чек заказа  между группами A и B статистически различается


```python
#Создаем переменные по очищенным данным по граппам A и B

revenueAfiltered = (
    ordersPerUsers[ordersPerUsers['group'] == 'A'][['date', 'revenue_per_user']]
    .groupby('date', as_index=False)
    .agg({'revenue_per_user': 'sum'}))

revenueAfiltered.columns = ['date', 'revenue_a']

revenueBfiltered = (
    ordersPerUsers[ordersPerUsers['group'] == 'B'][['date', 'revenue_per_user']]
    .groupby('date', as_index=False)
    .agg({'revenue_per_user': 'sum'}))

revenueBfiltered.columns = ['date', 'revenue_b']

dataRevenueFiltered = (
    revenueAfiltered.merge(
        revenueBfiltered, left_on='date', right_on='date', how='left'
    ))
```


```python
sampleRevFiltA = pd.concat([revenueAfiltered['revenue_a'], 
                           pd.Series(0, index=np.arange(
                               dataRevenueFiltered['revenue_a'].sum() - len(revenueAfiltered['revenue_a'])), 
                                     name='orders',),],axis=0,)

sampleRevFiltB = pd.concat([revenueBfiltered['revenue_b'], 
                           pd.Series(0, index=np.arange(
                               dataRevenueFiltered['revenue_b'].sum() - len(revenueBfiltered['revenue_b'])),
                                     name='orders',),],axis=0,)

display('p-value: ' +  "{0:.5f}".format(st.mannwhitneyu(
    dataRevenueFiltered['revenue_a'], dataRevenueFiltered['revenue_b'])[1]))
display('Относительный прирост: ' +  "{0:.3f}".format(
    dataRevenueFiltered['revenue_b'].mean() / dataRevenueFiltered['revenue_a'].mean() - 1))
```


    'p-value: 0.22064'



    'Относительный прирост: 0.138'


- P-value равен 22% и это говорит, что по "очищенным" данным нет статистически значимых различий в среднем чеке по группам.
- Относительный прирост в среднем чеке группы B вырос на почти 14% относительно группы A

# Выводы по исследованию.

## Подготовка данных:
1. Заменили названия столбцов, привели к нижнему регистру.
2. Преобразовали данные в столбцах с датой к формату datetime.
3. Обработали дубликаты в таблице orders по граппам A и B;
4. Добавили столбец 'сonversion' в новой кумулятивной переменной cumulativeData.

## Анализ A/B теста:

Проведенный A/B-тест имеет следующие результаты:

1. График кумулятивной выручки по группам:
   - Выручка в группе B выше, чем в группе A на протяжении всего теста.
   - В середине теста произошел резкий скачок выручки в группе B, что может указывать на наличие выбросов или крупных заказов.


2. График кумулятивного среднего чека по группам:
   - Средний чек в группе B в целом выше, чем в группе A.
   - В середине теста также наблюдается резкий скачок среднего чека в группе B.


3. График относительного изменения кумулятивного среднего чека группы B к группе A:
   - Присутствуют значительные колебания в начале теста, затем график стабилизируется около значения 0.25.
   - Возможно, в данных есть выбросы, влияющие на результаты.


4. График кумулятивного среднего количества заказов на посетителя по группам:
   - Кумулятивное среднее количество заказов на посетителя выше в группе B по сравнению с группой A.
   - В середине теста наблюдается рост количества заказов в обеих группах.


5. График относительного изменения кумулятивного среднего количества заказов на посетителя группы B к группе A:
   - В начале теста присутствуют колебания, затем график стабилизируется около значения 0.2.
   - Возможно, в данных есть выбросы, влияющие на результаты.


6. Точечный график количества заказов по пользователям:
   - Большинство пользователей делает только один заказ.
   - Есть пользователи с аномально большим количеством заказов.


7. 95-й и 99-й перцентили количества заказов на пользователя:
   - Не более 5% пользователей делает более 1 заказа.
   - Не более 1% пользователей делает более 2 заказов.
   - Можно выбрать значение 2 как границу для определения аномальных пользователей.


8. Точечный график стоимостей заказов:
   - Большинство заказов имеют стоимость до 20 000 у.е.
   - Есть заказы с аномально высокой стоимостью.


9. 95-й и 99-й перцентили стоимости заказов:
   - Не более 5% заказов имеют стоимость выше 26 785 у.е.
   - Не более 1% заказов имеют стоимость выше 53 904 у.е.
   - Можно выбрать значение 26 000 у.е. как границу для определения аномальных заказов.


10. Статистическая значимость различий в среднем количестве заказов на посетителя между группами по «сырым» данным:
    - Существуют статистически значимые различия между группами (p-value < 0.05).
    - Группа B показывает более высокое среднее количество заказов на посетителя.


11. Статистическая значимость различий в среднем чеке заказа между группами по «сырым» данным:
    - Статистически значимых различий между группами не обнаружено (p-value > 0.05).
    - Средний чек не отличается между группами.


12. Статистическая значимость различий в среднем количестве заказов на посетителя между группами по «очищенным» данным:
    - Существуют статистически значимые различия между группами (p-value < 0.05).
    - Группа B показывает более высокое среднее количество заказов на посетителя.


13. Статистическая значимость различий в среднем чеке заказа между группами по «очищенным» данным:
    - Статистически значимых различий между группами не обнаружено (p-value > 0.05).
    - Средний чек не отличается между группами.


Исходя из результатов анализа A/B-теста и статистической значимости, предлагается принять решение:

Основная задача нашего анализа была вырастить выручку либо через рост среднего чека, либо через количество заказов. В группе B мы зафиксировали стат.значимые различия, поэтому, я считаю, что следует остановить тест и зафиксировать победу группы B. Далее, предлагаю выбирать из бэклога задачи/проекты , которые могут повлиять на рост среднего чека и запускать их в A/B.


```python

```
