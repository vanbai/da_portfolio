# Портфолио аналитика данных / Data Analyst Portfolio

## Работа в Excel / Excel cases
- [Работа 1 - View on Google.Docs](https://docs.google.com/spreadsheets/d/1IX8HbL4OvF12qWsVYsTqIWjDiBBEu90q/edit?usp=drive_link&ouid=104984544571836462014&rtpof=true&sd=true)
- [Работа 2 - View on Google.Docs](https://docs.google.com/spreadsheets/d/19sWFiW75WHH3--SL71rZQG9U-Ornl451/edit?usp=sharing&ouid=104984544571836462014&rtpof=true&sd=true)
- [Работа 3 - View on Google.Docs](https://docs.google.com/spreadsheets/d/1CapNvgyIElkTDE7SH_dHHn21XLMDPR5_/edit?usp=sharing&ouid=104984544571836462014&rtpof=true&sd=true)

## Анализ данных с помощью Python / Data analysis with Python
### Пример 1
```
#Задача 1. Рассчитать средний возраст по всему списку age_lst
#Задача 2. Рассчитать средний возраст для посетителей, которые моложе 50 лет
#Задача 3. Обернуть предыдущий код в функцию, которая на вход принимает список и пороговое значение
#Задача 4. Рассчитать среднее значение для всех элементов списка, за исключением тех, которые лежат вне интервала [Q1-1,5IQR; Q3+1,5IQR]
#Задача 5. Обернуть предыдущий код в функцию, которая принимает на вход список и рассчитывает средний возраст, исключая из расчётов выбросы

age_lst = [23, 45, 18, 80, 22, 25, 34, 37, 41, 88, 19, 20, 18, 45, 41, 39, 31, 32, 22, 28, 37]

#### Задача 1 ####
avg_age = sum(age_lst)/len(age_lst)
print(f'Средний возраст {round(avg_age, 1)}')

#### Задача 2 ####
sum_age = 0
cnt_user = 0
for i in age_lst:
    if i < 50:
        sum_age += i
        cnt_user += 1
avg_age = sum_age/cnt_user
print(f'Средний возраст {round(avg_age, 1)}')

#### Задача 3 ####
def calc_avg_age(lst, thresh):
    sum_age = 0
    cnt_user = 0
    for i in lst:
        if i < thresh:
            sum_age += i
            cnt_user += 1
    avg_age = sum_age/cnt_user
    return f'Средний возраст {round(avg_age, 1)}'
calc_avg_age(age_lst, 50)

#### Задача 4 ####
import numpy as np
sum_age = 0
cnt_user = 0
q1 = np.percentile(age_lst, 25)
q3 = np.percentile(age_lst, 75)
iqr = q3 - q1
for i in age_lst:
    if (i > q1 - 1.5 * iqr) and (i < q3 + 1.5 * iqr):
        sum_age += i
        cnt_user += 1
avg_age = sum_age/cnt_user
print(f'Средний возраст {round(avg_age, 1)}')

#### Задача 5 ####
def calc_avg_age_adv(lst):
    try:
        sum_age = 0
        cnt_user = 0
        q1 = np.percentile(lst, 25)
        q3 = np.percentile(lst, 75)
        iqr = q3 - q1
        for i in age_lst:
            if (i > q1 - 1.5 * iqr) and (i < q3 + 1.5 * iqr):
                sum_age += i
                cnt_user += 1
        avg_age = sum_age/cnt_user
        return f'Средний возраст {round(avg_age, 1)}'
    except:
        print('Список должен содержать только числовые значения или не быть пустым')
calc_avg_age_adv(age_lst)
```
[Ссылка на файл JupyterNotebook](https://drive.google.com/file/d/1NgxQ0ERZVWgNVoK943JBmFe9QBVTQAhp/view?usp=sharing)

### Пример 2
```
#Задача 1. Изучить таблицу payments и провести разведочный анализ данных. Для этого нужно ответить на вопросы:
#- определить сколько в таблице строк и столбцов
#- вывести среднее, медиану, минимум, максимум и стандартное отклонение для колонки sum_payment
#- вывести количество нуллов. Можем ли мы заполнить нуллы значениями
#- вывести количество выбросов. Очистите данные от выбросов
#- построить график с распределением sum_payment (sns.histplot)
#Задача 2. Агрегировать данные по пользователю и месяцу
#Задача 3. Рассчитать выручку и количество тренировок в динамике по месяцам. Постройте графики
#Задача 4. Рассчитать LT и LTR. Подумайте, должны ли мы учитывать при расчете все когорты пользователей
#Задача 5. Оценить эффект от маркетинговой акции для выручки и для прибыли (при условии, что переменные косты на 1 занятие равны 800 руб. для индивидуальной тренировки и 200 руб. для групповой)

import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

payments = pd.read_csv('payments.csv')
payments.head()

#### Задача 1 ####
payments.shape
payments['sum_payment'].describe()
payments.isnull().sum()
payments.loc[(payments['sum_payment'].isnull()) & (payments['training_type'] == 'group'), 'sum_payment'] = 800
payments.loc[(payments['sum_payment'].isnull()) & (payments['training_type'] == 'indiv'), 'sum_payment'] = 1800
payments['date'] = pd.to_datetime(payments['date'], errors = 'coerce')
payments.loc[(payments['sum_payment'].isnull()) & (payments['training_type'] == 'indiv') & (payments['date'].dt.month == 5), 'sum_payment'] = 1600
payments = payments[~payments['date'].isnull()]
payments.isnull().sum()

sns.boxplot(payments['sum_payment'])

q1 = np.percentile(payments['sum_payment'], 25)
q3 = np.percentile(payments['sum_payment'], 75)
iqr = q3 - q1
lower_bound = q1 - 1.5 * iqr
upper_bound = q3 + 1.5 * iqr
print(lower_bound)
print(upper_bound)

payments_clean = payments.loc[payments['sum_payment'] < upper_bound].copy()
payments_clean.head()

sns.histplot(payments_clean['sum_payment'])

#### Задача 2 ####
payments_clean['mm'] = payments_clean['date'].dt.strftime('%Y-%m')
payments_clean.head()
pivot_pay = pd.pivot_table(payments_clean,\
                          values = 'sum_payment',\
                          index = ['id_user', 'mm'],\
                          columns = 'training_type',\
                          aggfunc = ['count', 'sum'],\
                          fill_value = 0).reset_index()
pivot_pay.head()

pivot_pay.columns = ['id_user', 'mm', 'cnt_group', 'cnt_indiv', 'sum_group', 'sum_indiv']
pivot_pay['cnt_total'] = pivot_pay['cnt_group'] + pivot_pay['cnt_indiv']
pivot_pay['sum_total'] = pivot_pay['sum_group'] + pivot_pay['sum_indiv']
pivot_pay.head()

#### Задача 3 ####
p_gr = pivot_pay.groupby('mm')[['cnt_group', 'cnt_indiv', 'cnt_total', 'sum_group', 'sum_indiv', 'sum_total']].sum().reset_index()
p_gr

plt.figure(figsize=(8, 5)) # задаем размер графика
plt.plot(p_gr['mm'], p_gr['cnt_group'], marker='o', label='Групповые') # рисуем линию для групповых тренировок
plt.plot(p_gr['mm'], p_gr['cnt_indiv'], marker='o', label='Индивидуальные') #рисуем линию для инд. тренировок
plt.plot(p_gr['mm'], p_gr['cnt_total'], marker='o', label='Всего')
plt.xlabel('Месяц') # подписываем ось х
plt.ylabel('Количество тренировок') # подписываем ось y
plt.title('Динамика количества тренировок по месяцам') #подписываем график
plt.legend() #добавляем легенду
plt.grid(True) # добавляем сетку
plt.ylim(bottom=0)  # начинаем ось Y с нуля
plt.xticks(rotation=45)  # поворот подписей на оси X
plt.show() # выводим график на экран

plt.figure(figsize=(8, 5))
plt.plot(p_gr['mm'], p_gr['sum_group'], marker='o', label='Групповые')
plt.plot(p_gr['mm'], p_gr['sum_indiv'], marker='o', label='Индивидуальные')
plt.plot(p_gr['mm'], p_gr['sum_total'], marker='o', label='Всего')
plt.xlabel('Месяц')
plt.ylabel('Выручка')
plt.title('Динамика выручки по месяцам')
plt.legend()
plt.grid(True)
plt.ylim(bottom=0)
plt.xticks(rotation=45)
plt.show()

#### Задача 4 ####
pivot_pay['min_mm'] = pivot_pay.groupby('id_user')['mm'].transform('min')
pivot_pay['max_mm'] = pivot_pay.groupby('id_user')['mm'].transform('max')
pivot_pay.head()

p_ltr = pivot_pay.groupby('id_user').agg(ltr = ('sum_total', 'sum')).reset_index()
print(round(p_ltr['ltr'].mean(), 2))
print(p_ltr['ltr'].median())

p_lt = pivot_pay.groupby('id_user').agg(n_mm = ('id_user', 'count')).reset_index()
print(round(p_ltr2['n_mm'].mean(), 2))
print(p_ltr2['n_mm'].median())

clean_p = pivot_pay.loc[pivot_pay['min_mm'].isin(['2023-01', '2023-02', '2023-03', '2023-04'])]
clean_ltr = clean_p.groupby('id_user').agg(ltr = ('sum_total', 'sum')).reset_index()
print(round(clean_ltr['ltr'].mean(), 2))
print(clean_ltr['ltr'].median())

p_lt2 = clean_p.groupby('id_user').agg(n_mm = ('id_user', 'count')).reset_index()
print(round(p_lt2['n_mm'].mean(), 2))
print(p_lt2['n_mm'].median())

#### Задача 5 ####
cnt_ind_apr = p_gr.loc[p_gr['mm'] == '2023-04', 'cnt_indiv'].iloc[0]
cnt_gr_apr = p_gr.loc[p_gr['mm'] == '2023-04', 'cnt_group'].iloc[0]
cnt_ind_may = p_gr.loc[p_gr['mm'] == '2023-05', 'cnt_indiv'].iloc[0]
cnt_gr_may = p_gr.loc[p_gr['mm'] == '2023-05', 'cnt_group'].iloc[0]
cnt_ind_june = p_gr.loc[p_gr['mm'] == '2023-06', 'cnt_indiv'].iloc[0]
cnt_gr_june = p_gr.loc[p_gr['mm'] == '2023-06', 'cnt_group'].iloc[0]

# факт. выручка в мае
rev_may_fact = p_gr.loc[p_gr['mm'] == '2023-05', 'sum_total'].iloc[0]

# гип. выручка в мае
rev_may_hyp = (cnt_ind_apr + cnt_ind_june)*0.5*1800 + (cnt_gr_apr + cnt_gr_june)*0.5*800

print(rev_may_fact)
print(rev_may_hyp)

print(f'Прирост выручки в результате проведения акции составил: {rev_may_fact - rev_may_hyp} руб.')

# факт. прибыль в мае
prof_may_fact = p_gr.loc[p_gr['mm'] == '2023-05', 'cnt_indiv'].iloc[0] * (1600 - 800) +\
    p_gr.loc[p_gr['mm'] == '2023-05', 'cnt_group'].iloc[0] * (800 - 200)

# гип. прибыль в мае
prof_may_hyp = (cnt_ind_apr + cnt_ind_june)*0.5*(1800-800) + (cnt_gr_apr + cnt_gr_june)*0.5*(800-200)

print(prof_may_fact)
print(prof_may_hyp)

print(f'Прирост прибыли в результате проведения акции составил: {prof_may_fact - prof_may_hyp} руб.')
```
[Ссылка на файл JupyterNotebook](https://drive.google.com/file/d/1WQi0RCvX26QX3Dbb1jIqeBe6qCyuuyJ7/view?usp=sharing)

### Примеры других работ
[Анализ сегментов и клиентской базы - Ссылка на файл JupyterNotebook](https://drive.google.com/file/d/1OfgbD0bgkh61XACdacC0QwhbZo9JBHs7/view?usp=sharing)

[Анализ A/B теста и создание Excel-калькулятора на основе результатов анализа - Ссылка на файл JupyterNotebook](https://drive.google.com/drive/folders/1Dhpvwe1q6QIoO5LA50eNXPD10ureJHCo?usp=sharing)

## Работа с запросами SQL / SQL cases
### Расчет средней продолжительности игровой сессии и расчет будущей когорты
```select cohort
      , avg(gs.end_session - gs.start_session) as avg_time_of_session
from (select id_user
            , case when date_trunc('month', reg_date) in ('2022-11-01', '2022-12-01') then '11-12.2022' else 'Остальньные когорты' end as cohort
       from skygame.users
     ) c
     join skygame.game_sessions gs
         on c.id_user = gs.id_user
where gs.end_session - gs.start_session > interval '5 minute'
group by cohort


with cnt_refs as
(select u.id_user
    , count(r.ref_reg) as shares_per_user
    , sum(r.ref_reg) as cnt_refs
from skygame.users u
    full join skygame.referral r
        on u.id_user = r.id_user
group by u.id_user
),
k_factor_calc as
(select avg(shares_per_user) as avg_shares_per_user
     , sum(cnt_refs) / sum(shares_per_user)::float as refs_ratio
     , avg(shares_per_user) * (sum(cnt_refs) / sum(shares_per_user)) as k_factor
from cnt_refs
),
volume_of_cohorts as
(select avg(coh_vol) as avg_cnt from
(select date_trunc('month', reg_date) as cohort
     , count(*) as coh_vol
from skygame.users
group by cohort) t
)


select avg_cnt * k_factor as future_cohort
from volume_of_cohorts, k_factor_calc
```