# Проект по SQL

# Описание
В условиях глобальной пандемии коронавируса, которая кардинально изменила привычный порядок жизни, наблюдается рост интереса к чтению книг. Это стало заметно как среди читателей, так и среди предпринимателей, стремящихся адаптироваться к новым условиям. В результате, наша компания приобрела крупный сервис для чтения книг по подписке, который предоставляет доступ к обширной базе данных, содержащей информацию о книгах, издательствах, авторах и пользовательских обзорах. Данные этой базы представляют собой уникальный ресурс для анализа текущих трендов в литературе и предпочтений пользователей.


# Цель исследования
Цель данного проекта заключается в анализе базы данных книг с целью выявления ключевых инсайтов, которые помогут сформулировать ценностное предложение для нового продукта. Мы стремимся понять, какие книги наиболее популярны среди пользователей, какие издательства и авторы выделяются на фоне других, а также какие факторы влияют на качество пользовательских обзоров.


# Задачи исследования

1. Подсчитать количество книг, выпущенных после 1 января 2000 года.
2. Для каждой книги определить количество обзоров и среднюю оценку.
3. Выявить издательство, которое выпустило наибольшее количество книг, толще 50 страниц.
4. Определить автора с самой высокой средней оценкой книг, учитывая только те книги, которые получили более 50 оценок.
5. Рассчитать среднее количество обзоров от пользователей, которые поставили более 48 оценок.

# Этапы исследования

1. Загрузить библиотеки и вывести общую информацию о данных.
2. Анализ данных: Проведение расчетов и статистических анализов для решения поставленных задач.
3. Подготовка отчета: Составление итогового отчета с рекомендациями для команды разработки и маркетинга на основе проведенного анализа.


## Загрузка данных.


```python
# Импортируем библиотеки
import pandas as pd
from sqlalchemy import text, create_engine

# Устанавливаем параметры
db_config = {'user': 'praktikum_student', # имя пользователя
'pwd': 'Sdf4$2;d-d30pp', # пароль
'host': 'rc1b-wcoijxj3yxfsf3fs.mdb.yandexcloud.net',
'port': 6432, # порт подключения
'db': 'data-analyst-final-project-db'} # название базы данных
connection_string = 'postgresql://{user}:{pwd}@{host}:{port}/{db}'.format(**db_config)

# Сохраняем коннектор
engine = create_engine(connection_string, connect_args={'sslmode':'require'})
```


```python
# Функция для выполнения SQL-запроса и вывода результата

def fetch_table_data(query):
    with engine.connect() as con:
        return pd.io.sql.read_sql(sql=text(query), con=con)


```


```python
# Получение данных из каждой таблицы
queries = {
    "books": '''SELECT * FROM books LIMIT 5''',
    "authors": '''SELECT * FROM authors LIMIT 5''',
    "publishers": '''SELECT * FROM publishers LIMIT 5''',
    "ratings": '''SELECT * FROM ratings LIMIT 5''',
    "reviews": '''SELECT * FROM reviews LIMIT 5'''
}


# Получаем данные и выводим их
for table_name, query in queries.items():
    print(f"Данные из таблицы {table_name}:")
    data = fetch_table_data(query)
    display(data)
    

```

    Данные из таблицы books:



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
      <th>book_id</th>
      <th>author_id</th>
      <th>title</th>
      <th>num_pages</th>
      <th>publication_date</th>
      <th>publisher_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>546</td>
      <td>'Salem's Lot</td>
      <td>594</td>
      <td>2005-11-01</td>
      <td>93</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>465</td>
      <td>1 000 Places to See Before You Die</td>
      <td>992</td>
      <td>2003-05-22</td>
      <td>336</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>407</td>
      <td>13 Little Blue Envelopes (Little Blue Envelope...</td>
      <td>322</td>
      <td>2010-12-21</td>
      <td>135</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>82</td>
      <td>1491: New Revelations of the Americas Before C...</td>
      <td>541</td>
      <td>2006-10-10</td>
      <td>309</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>125</td>
      <td>1776</td>
      <td>386</td>
      <td>2006-07-04</td>
      <td>268</td>
    </tr>
  </tbody>
</table>
</div>


    Данные из таблицы authors:



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
      <th>author_id</th>
      <th>author</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>A.S. Byatt</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Aesop/Laura Harris/Laura Gibbs</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Agatha Christie</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Alan Brennert</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Alan Moore/David   Lloyd</td>
    </tr>
  </tbody>
</table>
</div>


    Данные из таблицы publishers:



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
      <th>publisher_id</th>
      <th>publisher</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Ace</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Ace Book</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Ace Books</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Ace Hardcover</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Addison Wesley Publishing Company</td>
    </tr>
  </tbody>
</table>
</div>


    Данные из таблицы ratings:



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
      <th>rating_id</th>
      <th>book_id</th>
      <th>username</th>
      <th>rating</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>1</td>
      <td>ryanfranco</td>
      <td>4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>1</td>
      <td>grantpatricia</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>1</td>
      <td>brandtandrea</td>
      <td>5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>2</td>
      <td>lorichen</td>
      <td>3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>2</td>
      <td>mariokeller</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>


    Данные из таблицы reviews:



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
      <th>review_id</th>
      <th>book_id</th>
      <th>username</th>
      <th>text</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>1</td>
      <td>brandtandrea</td>
      <td>Mention society tell send professor analysis. ...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>1</td>
      <td>ryanfranco</td>
      <td>Foot glass pretty audience hit themselves. Amo...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>2</td>
      <td>lorichen</td>
      <td>Listen treat keep worry. Miss husband tax but ...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>3</td>
      <td>johnsonamanda</td>
      <td>Finally month interesting blue could nature cu...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>3</td>
      <td>scotttamara</td>
      <td>Nation purpose heavy give wait song will. List...</td>
    </tr>
  </tbody>
</table>
</div>


- В таблице **books** содержится информация о книгах, включая идентификаторы, авторов, количество страниц, дату публикации и издателя.
- Таблица **authors** содержит информацию об авторах, включая их идентификаторы и имена.
- Таблица **publishers** содержит информацию о издателях, включая их идентификаторы и названия.
- Таблица **ratings** отображает оценки книг пользователями, связывая их с идентификаторами книг и именами пользователей.
- Таблица **reviews** содержит отзывы на книги, также связывая их с идентификаторами книг и именами пользователей.


## Анализ данных.

### Количество книг, вышедших после 1 января 2000 года.


```python
# Посчитаем количество книг, вышедших после 1 января 2000 года

query = '''
SELECT COUNT(*) AS count_books
FROM books
WHERE publication_date > '2000-01-01';
'''

count_books = fetch_table_data(query)

# Выводим результат
print("Количество книг, вышедших после 1 января 2000 года:", count_books['count_books'][0])


```

    Количество книг, вышедших после 1 января 2000 года: 819


### Количество обзоров и средняя оценка для каждой книги.


```python
# Посмотрим общее количество уникальных отзывов
query_total_reviews = '''
SELECT COUNT(DISTINCT review_id) AS total_reviews
FROM reviews;
'''

# Выводим данные

total_reviews_data = fetch_table_data(query_total_reviews)
total_reviews_count = total_reviews_data['total_reviews'].iloc[0]

total_reviews_count
```




    2793




```python
# Посчитаем уникальные обзоры на книги
# Подсчет количества обзоров и средней оценки для каждой книги
query_reviews_ratings = '''
SELECT 
    b.book_id,
    b.title,
    COUNT(DISTINCT r.review_id) AS review_count,  -- Используем DISTINCT для уникальных отзывов
    AVG(rt.rating) AS average_rating
FROM 
    books b
LEFT JOIN 
    reviews r ON b.book_id = r.book_id
LEFT JOIN 
    ratings rt ON b.book_id = rt.book_id
GROUP BY 
    b.book_id, b.title;
'''

# Получаем данные и выводим их
reviews_ratings_data = fetch_table_data(query_reviews_ratings)

print(reviews_ratings_data['review_count'].sum())
display(reviews_ratings_data.sort_values(by = ['review_count', 'average_rating'], ascending = False).head(10))

```

    2793



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
      <th>book_id</th>
      <th>title</th>
      <th>review_count</th>
      <th>average_rating</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>947</th>
      <td>948</td>
      <td>Twilight (Twilight  #1)</td>
      <td>7</td>
      <td>3.662500</td>
    </tr>
    <tr>
      <th>301</th>
      <td>302</td>
      <td>Harry Potter and the Prisoner of Azkaban (Harr...</td>
      <td>6</td>
      <td>4.414634</td>
    </tr>
    <tr>
      <th>298</th>
      <td>299</td>
      <td>Harry Potter and the Chamber of Secrets (Harry...</td>
      <td>6</td>
      <td>4.287500</td>
    </tr>
    <tr>
      <th>655</th>
      <td>656</td>
      <td>The Book Thief</td>
      <td>6</td>
      <td>4.264151</td>
    </tr>
    <tr>
      <th>733</th>
      <td>734</td>
      <td>The Glass Castle</td>
      <td>6</td>
      <td>4.206897</td>
    </tr>
    <tr>
      <th>496</th>
      <td>497</td>
      <td>Outlander (Outlander  #1)</td>
      <td>6</td>
      <td>4.125000</td>
    </tr>
    <tr>
      <th>749</th>
      <td>750</td>
      <td>The Hobbit  or There and Back Again</td>
      <td>6</td>
      <td>4.125000</td>
    </tr>
    <tr>
      <th>694</th>
      <td>695</td>
      <td>The Curious Incident of the Dog in the Night-Time</td>
      <td>6</td>
      <td>4.081081</td>
    </tr>
    <tr>
      <th>778</th>
      <td>779</td>
      <td>The Lightning Thief (Percy Jackson and the Oly...</td>
      <td>6</td>
      <td>4.080645</td>
    </tr>
    <tr>
      <th>962</th>
      <td>963</td>
      <td>Water for Elephants</td>
      <td>6</td>
      <td>3.977273</td>
    </tr>
  </tbody>
</table>
</div>


- Больше всего отзывов у книги Twilight - 7. Ср.оценка - 3.66
- Harry Potter and the Prisoner of Azkaban - 6. Ср.оценка - 4.41
- Harry Potter and the Chamber of Secrets - 6. Ср.оценка - 4.28

###  Издательство, которое выпустило наибольшее число книг толще 50 страниц.


```python
# Запрос для определения издательства с наибольшим количеством книг толще 50 страниц
query_top_publisher = '''
SELECT 
    p.publisher_id,
    p.publisher AS publisher_name,
    COUNT(b.book_id) AS book_count
FROM 
    books b
JOIN 
    publishers p ON b.publisher_id = p.publisher_id
WHERE 
    b.num_pages > 50
GROUP BY 
    p.publisher_id, p.publisher
ORDER BY 
    book_count DESC
LIMIT 1;
'''

# Получаем данные и выводим их

top_publisher_data = fetch_table_data(query_top_publisher)
display(top_publisher_data)

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
      <th>publisher_id</th>
      <th>publisher_name</th>
      <th>book_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>212</td>
      <td>Penguin Books</td>
      <td>42</td>
    </tr>
  </tbody>
</table>
</div>


- Издательство с наибольшим количеством книг (толще 50 страниц) - Penguin Books 
- Выпущено 42 книги.

### Автор с самой высокой средней оценкой книг.


```python
# Определим автора с самой высокой средней оценкой книг

query = '''
SELECT a.author, AVG(r.rating) AS average_rating
FROM authors a
JOIN books b ON a.author_id = b.author_id
JOIN ratings r ON b.book_id = r.book_id
GROUP BY a.author
HAVING COUNT(DISTINCT r.rating_id) >= 50
ORDER BY average_rating DESC
LIMIT 1;
'''

# Выведем результат

result = fetch_table_data(query)
display(result)

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
      <th>author</th>
      <th>average_rating</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Diana Gabaldon</td>
      <td>4.3</td>
    </tr>
  </tbody>
</table>
</div>



```python
# Проверим количество оценок у книг Дианы Габалдон

author_name = 'Diana Gabaldon'

query_check_ratings = f'''
SELECT 
    b.title,
    COUNT(r.rating_id) AS rating_count
FROM 
    authors a
JOIN 
    books b ON a.author_id = b.author_id
JOIN 
    ratings r ON b.book_id = r.book_id
WHERE 
    a.author = '{author_name}'
GROUP BY 
    b.title;
'''

# Получим данные, выведем на экран

ratings_count_data = fetch_table_data(query_check_ratings)


# Выводим количество оценок для каждой книги Дианы Габалдон

print(f"Количество оценок для книг {author_name}:")
display(ratings_count_data)


```

    Количество оценок для книг Diana Gabaldon:



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
      <th>title</th>
      <th>rating_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>A Breath of Snow and Ashes (Outlander  #6)</td>
      <td>4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Dragonfly in Amber (Outlander  #2)</td>
      <td>8</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Drums of Autumn (Outlander  #4)</td>
      <td>5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Lord John and the Private Matter (Lord John Gr...</td>
      <td>2</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Outlander (Outlander  #1)</td>
      <td>24</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Voyager (Outlander  #3)</td>
      <td>7</td>
    </tr>
  </tbody>
</table>
</div>


- У книг Diana Gabaldon нет 50 оценок


```python
# Исправим код, используем вложенный запрос, чтобы сначала отфильтровать книги с достаточным количеством оценок
# Определим автора с самой высокой средней оценкой книг, учитывая условие о 50 оценках
query = '''
SELECT a.author, AVG(r.rating) AS average_rating
FROM authors a
JOIN books b ON a.author_id = b.author_id
JOIN ratings r ON b.book_id = r.book_id
WHERE b.book_id IN (
    SELECT b_inner.book_id
    FROM books b_inner
    JOIN ratings r_inner ON b_inner.book_id = r_inner.book_id
    GROUP BY b_inner.book_id
    HAVING COUNT(r_inner.rating_id) >= 50
)
GROUP BY a.author
ORDER BY average_rating DESC
LIMIT 1;
'''

# Выводим результат
result = fetch_table_data(query)
display(result)


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
      <th>author</th>
      <th>average_rating</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>J.K. Rowling/Mary GrandPré</td>
      <td>4.287097</td>
    </tr>
  </tbody>
</table>
</div>


- Автор с самой высокой средней оценкой книг (только книги с 50 и более оценками) - J.K. Rowling (4.28)

### Среднее количество обзоров от пользователей.


```python
# Посчитаем среднее количество обзоров от пользователей, которые поставили больше 48 оценок

query_reviews = '''
SELECT AVG(review_count) AS average_reviews
FROM (
    SELECT filtered_users.username, COUNT(DISTINCT rev.review_id) AS review_count
    FROM (
        SELECT username
        FROM ratings
        GROUP BY username
        HAVING COUNT(rating_id) > 48
    ) AS filtered_users
    LEFT JOIN reviews rev ON filtered_users.username = rev.username
    GROUP BY filtered_users.username
) AS user_reviews;
'''


# Получаем данные и выводим результат

result_reviews = fetch_table_data(query_reviews)
result_reviews


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
      <th>average_reviews</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>24.0</td>
    </tr>
  </tbody>
</table>
</div>



- Среднее количество обзоров от пользователей, которые поставили больше 48 оценок - 24 обзора.

## Выводы и рекомендации.

1) Количество книг, вышедших после 1 января 2000 года: 819. Это свидетельствует о значительном росте публикаций в последние два десятилетия.

   - **Рекомендации** : обратить внимание на современные тренды и предпочтения читателей, чтобы эффективно продвигать новые издания.


2) Больше всего отзывов у следующих книг:

    - Twilight - 7. Ср.оценка - 3.66
    - Harry Potter and the Prisoner of Azkaban - 6. Ср.оценка - 4.41
    - Harry Potter and the Chamber of Secrets - 6. Ср.оценка - 4.28

   - **Рекомендации** : учитывая высокую активность пользователей в отзывах на указанные книги, следует рассмотреть возможность маркетинговых кампаний, связанных с этими произведениями.
    
3) Издательство с наибольшим количеством книг (толще 50 страниц) - Penguin Books (Выпущено 42 книги). Это издательство демонстрирует высокую продуктивность и разнообразие изданий.

   - **Рекомендации** : рассмотреть возможность сотрудничества с Penguin Books для продвижения их новых изданий
    
4) Автор с самой высокой средней оценкой книг (только книги с 50 и более оценками) - J.K. Rowling (4.28). Это указывает на высокий уровень удовлетворенности читателей ее произведениями.

   - **Рекомендации** : организовать мероприятия с участием J.K. Rowling или создать контент, связанный с ее работами, чтобы привлечь внимание к ее книгам и повысить интерес к жанру.


5) Среднее количество обзоров от пользователей, которые поставили больше 48 оценок - 24. Это показатель активности пользователей, который может быть использован для оценки вовлеченности читателей.

   - **Рекомендации** : Разработать программы лояльности или поощрения для активных рецензентов, чтобы увеличить количество отзывов и повысить интерес к новым изданиям.



```python

```
