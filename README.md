

<p align="center">
  <img src="https://user-images.githubusercontent.com/53406289/175799091-89e0fa62-c2e6-4eb2-bcab-6ad9d0d3a231.png" alt="Структура сети"/>
</p>
<!-- Добавить изображение с классифицированной картинкой -->

---
# Доработка нейросети resnet34 для автоматизации описания изображений на основе тегов
- Автор кейса: Цифровой прорыв - Уральский федеральный округ - ЛизаАлерт
- Кейс: "ИИ фотобанк для поискового отряда"
- Решают: команда **I PRO**
- [Репозиторий - фронтенд](https://github.com/i-pro-lizaalert/frontend-nextjs)
- [Репозиторий - бекенд](https://github.com/i-pro-lizaalert/backend-fastapi)
- [Репозиторий - нейронная сеть](https://github.com/i-pro-lizaalert/ml-torch-fastapi)
---

## Постановка задачи
Задача присвоения тегов изображению оттносится к задаче многолейбельной классификации (Multilabel classification). 
На основане изображения, мы должны присвоить ему несколько различных классов. В кокретно нашей задаче было разделение
на 11 признаков, 3 из которых являлись каегориальными фичами с 5, 4 и 3 категориями соответственно - в итоге на выходе 16 тегов.

_Список призанков: 'Название', 'Время суток', 'Время года', 'Местность', 'Авиа', 'Автомобили', 'БПЛА', 'Водолаз', 'Кинолог', 'Кони', 'Объятия', 'Шерп'_

## Наш подход
При решении задач компьютерного зрения, важно обращать внимание на уже готовые решения, проверенные временем. Так поступили и мы, решив доработать классификатор resnet34 под эту задачу. Такая хорошая модель позволяет нам извлекать из картинок фичи, которые можно использовать для обучения более простых по структуре классификаторов. Так, на каждый признак разбиения по тегам в нашей модели приходится по линейному полносвязному слою, никак не связанным с остальными классификаторами - таким образом классификация времени суток не будет зависеть от классификаци, допустим местности, тем самым позволяя учиться классификаторам, что позволит улучшить классическую модель.


<p align="center">
  <img src="https://user-images.githubusercontent.com/53406289/175800130-4bc673ae-41f4-438b-b278-1456d698af74.png" alt="Лосс меме"/>
</p>

## Работа сети
На вход нейронной сети поступает картинка 256 на 256, который отправляются в основу сети, resnet34. Его задача заключается в извлечения признаковых описаний  Выход resnet34 подается на вход 11-ти линейным полносвязным слоям - по слою-"классификатору" на каждый признак. Каждый полносвязный слой включает в себя 512 нейронов и имеет количество выходов, равное количеству возможных значений признака. В итоге у нейронной сети получается 16 логитов, отражающих вероятность тега.  

## Обучение модели
При обучении мы добились высоких результатов - на валидационной части датасета метрика f1-score составила 0.9378 (НЕ на приватной части датасета, данные по качеству модели на ней будут оглашены в конце соревнования). Использовали CrossEntropyLoss для каждого классификатора, оптимизатор Adam. Кроме того, при обучении возникали трудности в использовании оперативной памяти, подробно процесс можно посмотреть в [ноутбуке]( https://github.com/i-pro-lizaalert/ml-torch-fastapi/blob/main/docs/LizaAlertNet.ipynb ). Для достижения таких результатов нам хватило 200 эпох (~2500 изображений на train, батч 128).

<p align="center">
  <img src="https://user-images.githubusercontent.com/53406289/175799963-0bd0ead2-4b28-449f-8934-6dd69290dda1.png" alt="Лосс меме"/>
</p>

## Подробная структура сети
Вы можете найти её summary в [этом файле](https://github.com/i-pro-lizaalert/ml-torch-fastapi/blob/main/docs/model_summary.txt). Также, имеется схема [backward pass'а](https://github.com/i-pro-lizaalert/ml-torch-fastapi/blob/main/docs/backward_pass_example.png).

## Дополнительные удобства
- Модель легко расширяется, при обучении дополнительных классификаторов не требуется переобучать модель целиком
- Обёртка в микросервис, позволяющая легко интегрировать его в существующую систему 

# Самое интересное здесь!
_Демо_ модели доступно по [ссылке](#) (если ещё не добавил - ищите на сайте http://i-pro.duckdns.org)

## Запуск микросервиса
Чтобы запустить микросервис достаточно установить зависимости, скачать веса и запустить скрипт. Для запуска требуется Python3.
По стандарту сервер запускается на localhost:8081
```bash
# Установка зависимостей
pip3 install -r requirements.txt
# Запуск сервера
python3 server.py
```


---
# I PRO
- Егор Смурыгин - _продукт-менеджер_
- Егор Голубев - _бекенд-разработчик_
- Роман Мамаев - _фронтенд-разработчик_
- Андрей Баранов - _UX/UI Designer_
- Тампио Илья - _ML-специалист_



