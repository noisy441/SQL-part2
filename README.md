# Домашнее задание к занятию «SQL. Часть 2» - Дудин Сергей Васильнвич

### Инструкция по выполнению домашнего задания

1. Сделайте fork [репозитория c шаблоном решения](https://github.com/netology-code/sys-pattern-homework) к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/gitlab-hw или https://github.com/имя-вашего-репозитория/8-03-hw).
2. Выполните клонирование этого репозитория к себе на ПК с помощью команды `git clone`.
3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
   - впишите вверху название занятия и ваши фамилию и имя;
   - в каждом задании добавьте решение в требуемом виде: текст/код/скриншоты/ссылка;
   - для корректного добавления скриншотов воспользуйтесь инструкцией [«Как вставить скриншот в шаблон с решением»](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md);
   - при оформлении используйте возможности языка разметки md. Коротко об этом можно посмотреть в [инструкции по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md).
4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`).
5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
6. Любые вопросы задавайте в чате учебной группы и/или в разделе «Вопросы по заданию» в личном кабинете.

Желаем успехов в выполнении домашнего задания.

---

Задание можно выполнить как в любом IDE, так и в командной строке.

### Задание 1

Одним запросом получите информацию о магазине, в котором обслуживается более 300 покупателей, и выведите в результат следующую информацию: 
- фамилия и имя сотрудника из этого магазина;
- город нахождения магазина;
- количество пользователей, закреплённых в этом магазине.

### Решение 1

С помощью SELECT определяем данные которые хотим получить, а именно фамилию и имя сотрудника, город магазина и количество клиентов. FROM store st указывает что основная таблица store. JOIN связывает таблицы.  GROUP BY группирует результаты по ID магазина ID сотрудника, фамилии имени и городу. HAVING фильтрует группы оставляя только те, где количество клиентов более 300

```

SELECT 
    s.last_name AS staff_last_name,
    s.first_name AS staff_first_name,
    c.city AS store_city,
    COUNT(cust.customer_id) AS customer_count
FROM store st
JOIN staff s ON st.manager_staff_id = s.staff_id
JOIN address a ON st.address_id = a.address_id
JOIN city c ON a.city_id = c.city_id
JOIN customer cust ON st.store_id = cust.store_id
GROUP BY st.store_id, s.staff_id, s.last_name, s.first_name, c.city
HAVING COUNT(cust.customer_id) > 300;

```

![Решение 1](https://github.com/noisy441/SQL-part2/blob/main/img/img1.png)

---

### Задание 2

Получите количество фильмов, продолжительность которых больше средней продолжительности всех фильмов.

### Решение 2

Подсчитываем количество записей в таблице film и присваиваем имя numbers_of_film результатам вывода с помощью SELECT COUNT(*) AS number_of_films FROM film.
Далее с помощью WHERE и подзапроса вычисляем среднюю продолжительность фильмов (lenght)  и количество фильмов с продолжительностью выше средней.  
Весь запрос выглядит так:

```

SELECT COUNT(*) AS number_of_films
FROM film
WHERE length > (
    SELECT AVG(length)
    FROM film
    WHERE length IS NOT NULL
);

```

![Решение 2](https://github.com/noisy441/SQL-part2/blob/main/img/img2.png)


---

### Задание 3

Получите информацию, за какой месяц была получена наибольшая сумма платежей, и добавьте информацию по количеству аренд за этот месяц.

### Решение 3

В блоке SELECT выберем год месяц общую суммуу платежей и подсчитаем уникальные аренды. Блок FROM указывает, что данные мы будем брать из таблицы payment. С помощью JOIN rental r ON p.rental_id = r.rental_id — соединяем таблицу платежей с таблицей аренд по идентификатору аренды. Группируем платежи по месяцу и году в блоке GROUP BY, далее в ORDER BY сортируем результаты по убыванию общей суммы платежей и при помощи LIMIT 1 выводим первую строку отсортированного результата.
Весь запрос ниже.

```

SELECT 
    DATE_FORMAT(p.payment_date, '%Y') AS payment_year,
    DATE_FORMAT(p.payment_date, '%m') AS payment_month,
    SUM(p.amount) AS total_payment_amount,
    COUNT(DISTINCT r.rental_id) AS rental_count
FROM payment p
JOIN rental r ON p.rental_id = r.rental_id
GROUP BY 
    DATE_FORMAT(p.payment_date, '%Y'), 
    DATE_FORMAT(p.payment_date, '%m')
ORDER BY total_payment_amount DESC
LIMIT 1;

```

![Решение 3](https://github.com/noisy441/SQL-part2/blob/main/img/img3.png)

---

Вариант решения без использования таблицы rental 

```

SELECT 
    DATE_FORMAT(p.payment_date, '%Y') AS payment_year,
    DATE_FORMAT(p.payment_date, '%m') AS payment_month,
    SUM(p.amount) AS total_payment_amount,
    COUNT(DISTINCT p.rental_id) AS rental_count
FROM payment p
GROUP BY 
    DATE_FORMAT(p.payment_date, '%Y'), 
    DATE_FORMAT(p.payment_date, '%m')
ORDER BY total_payment_amount DESC
LIMIT 1;

```

## Дополнительные задания (со звёздочкой*)
Эти задания дополнительные, то есть не обязательные к выполнению, и никак не повлияют на получение вами зачёта по этому домашнему заданию. Вы можете их выполнить, если хотите глубже шире разобраться в материале.

### Задание 4*

Посчитайте количество продаж, выполненных каждым продавцом. Добавьте вычисляемую колонку «Премия». Если количество продаж превышает 8000, то значение в колонке будет «Да», иначе должно быть значение «Нет».

### Решение 4

В блоке SELECT выбираем идентификатор сотрудника и считаем обшее количество продаж для каждого. CASE проверяет было ли продаж более 8000 и если было то возвращает ДА если не было то НЕТ. END AS заносит резкльтат в колонку bonus. GROUP BY группирует результат по идентификатору сотрудника, а ORDER BY сортирует по убыванию rental_count. Итоговый запрос выглядит так:

```

SELECT 
    staff_id,
    COUNT(rental_id) AS rental_count,
    CASE 
        WHEN COUNT(rental_id) > 8000 THEN 'Да'
        ELSE 'Нет'
    END AS bonus
FROM rental
GROUP BY staff_id
ORDER BY rental_count DESC;

```

![Решение 4](https://github.com/noisy441/SQL-part2/blob/main/img/img4.png)

---

### Задание 5*

Найдите фильмы, которые ни разу не брали в аренду.

### Решение 5 

Выберем в SELECT нахвание фильма и его ID из таблицы film
С помощью LEFT JOIN присоединим данные из таблиц rental и inventory. WHERE r.rental_id IS NULL отберет значения из результатов где rental_id равен NULL это и будет значить что фильм не брали в аренду. С помощью GROUP BY 
отсортируем вывод по ID и названию фильма. 

```
SELECT 
    f.film_id,
    f.title
FROM film f
LEFT JOIN inventory i ON f.film_id = i.film_id
LEFT JOIN rental r ON i.inventory_id = r.inventory_id
WHERE r.rental_id IS NULL
GROUP BY f.film_id, f.title;

```

![Решение 5](https://github.com/noisy441/SQL-part2/blob/main/img/img5.png)

---
