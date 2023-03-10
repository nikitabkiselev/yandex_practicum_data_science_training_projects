# Финальный проект. Построение модели предсказания температуры на металлургическом производстве 

### Статус проекта

`Завершен`

### Используемые инструменты

`Pandas`, `Catboost`, `Lightgbm`, `Sklearn`, `Xgboost`, `Numpy`, `Seaborn`, `Matplotlib`, `Plotly`

## Цель

Чтобы оптимизировать производственные расходы, металлургический комбинат ООО «Так закаляем сталь» решил уменьшить потребление электроэнергии на этапе обработки стали. Необходимо построить модель, которая предскажет температуру стали.

## Итоги

В данной работе для уменьшения энергопотребления нам необходимо было построить модель, которая предскажет температуру стали.

1. Этап предобработки и исследовательского анализа данных проведен полный перечень исследований исходя из объема и структуры полученной информации, в частности:
   - данные, содержащие дату и время технологического процесса во всех таблицах приведены к типу datetime;
   - произведена агрегация данных по ключу в таблицах, записи в которых не уникальны по ключам (электроды, замеры температуры);
   - для целей анализа добавлены новые признаки: количество циклов нагрева, общая мощность и энергия на нагрев стали, суммарный объем сыпучих материалов, суммарный объем проволочных материалов, длительность процессов нагрева, обработки сыпучими материалами и обработки проволочными материалами;
   - проведено удаление части данных по определенным критериям (реактивная мощность меньше нуля; начальная температура меньше 1300 град; материалы, использованные в единичных случаях; отсутствие замера конечной температуры; наличие операций после замера конечной температуры);
   - для целей дальнейшего построения и расчета в моделях данные соеденены по по ключу партии и типам связи - внутреннее соединение.

2. Этап обучения. Первоначально произведено разделение данных на обучающую и тестовую выборки в соотношении 4:1 (20% тестовой выборки). Впоследствии использованы следующие модели:
   - Линейные модели (LinearRegression):
        - произведено масштабирование признаков
        - оценка модели на кросс-валидации для линейной регрессии без регуляризации, LASSO и RIDGE. Для моделей LASSO и RIDGE случайный перебор гиперпараметра alpha, характеризующего степень регуляризации. Лучшее качество на кросс-валидации MAE=5.94 достигнуто при использовании модели LASSO c гиперпараметром 'alpha' = 0.0808
        - установлено, что наибольший вес линейной модели с L1-регуляризацией имеет начальная температура. Все параметры характеризующие параметры нагрева электродами, кроме общей мощности оказались значимыми. Расчетные признаки, характеризующие длительность процессов обработки также оказались значимыми. 
   - Случайный лес (RandomForestRegressor):
        - оценка модели на кросс-валидации с подбором гиперпараметров для параметров 'n_estimators': 1000, 'random_state': 12092022, достигнуто лучшее качество на кросс-валидации MAE=6.15
   - CatBoostRegressor:
        - оценка модели на кросс-валидации с подбором гиперпараметров позволила получить значение MAE=5.90  
   - Градиентный Бустинг (LGBMRegressor):
        - оценка модели на кросс-валидации c подбором гиперпараметров для параметров 'learning_rate': 0.08, 'num_leaves': 23, 'random_state': 12092022, достигнуто значение МАЕ=5.99 
    - XGBRegressor:
        - оценка модели на кросс-валидации c подбором гиперпараметров для параметров 'learning_rate': 0.09, 'random_state': 12092022, достигнуто значение МАЕ=5.90 

3. Вывод по результатам моделей на кросс-валидации
    - в связи с получением двух лучших результата на кросс-валидации при использовании моделей CatBoostRegressor (5.903196) и XGBRegressor (5.909792), разница в работе которых минимальна, обе эти модели были выбраны для проверки на тестовой выборке.

4. Вывод по этапу тестирования:
    - лучшей моделью по метрике MAE = 5.79 на тестовой выборке стал CatBoostRegressor. Это результат немного лучше, чем результат работы модели XGBRegressor (MAE = 5.99). Результаты обоих моделей показывают, что модели в среднем ошибаются на 5-6 градусов температуры. В результате обе указанные модели могуты быть рекомендованы для внедрения с приоритетом немного более точной модели CatBoostRegressor.
