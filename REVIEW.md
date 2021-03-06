### [GitHub репозиторий проекта](https://github.com/ProstoSawa/praktikum)

#### Общие рекомендации по [src/app.py](src/app.py) и [src/film_loader.py](src/film_loader.py):
- При оформлении git репозитория добавлять в проект файл с зависимостями `requirements.txt`. 
Пример: 
    - `pip freeze > requirements.txt`. [Ссылка](https://pip.pypa.io/en/stable/user_guide) на документацию.
- При оформлении git репозитория добавлять в проект файл с кратким текстовым пояснением 
для других разработчиков / пользователей. Пример: 
    - `README.md` в разметке Markdown или reStructuredText
- Не смешивать стиль кавычек в коде (одинарные и двойные), желательно использовать только один, который больше нравится. 
Пример некачественого оформления: 
    - [src/app.py](src/app.py#L32-L39) строки 32-39
    - [src/film_loader.py](src/film_loader.py#L99-L100) строки 99-100
- Файлам в проекте давать более читаемые и понятные имена. Например:
    - `app.py` переименовать в `films_api_app.py`, тогда не открывая файл будет понятно что это `api` для `films`
    - `film_loader.py` переименовать в `films_loader.py`,  тк речь идет о множестве фильмов
- Для данных о подключениях (адрес, порт, путь до файла данных, имя пользователя, пароль и т.д.) 
рекомендуется использовать переменные окружения и/или файлы конфигураций, а не писать непосредственно в коде. 
В качестве готовых библиотек можно использовать стандартный `configparser` 
или [python-dotenv](https://pypi.org/project/python-dotenv). 
Пример некачественого оформления:
    - [src/app.py](src/app.py#L55) строки 55
    - [src/app.py](src/app.py#L69) строки 69
    - [src/film_loader.py](src/film_loader.py#L13) строки 13
    - [src/film_loader.py](src/film_loader.py#L115) строки 115
- При разработке настоятельно рекомендуется следить за качеством, чистотой и стилистикой исходного кода 
согласно PEP, для этих целей для этих целей необходимо настроить. 
[pylint](https://www.pylint.org) или более строгие аналоги [black](https://github.com/psf/black).
Пример отчете pylint можно посмотреть в файлах:
    - для [src/app.py](src/app.py) в [app.py.txt](app.py.txt)
    - для [src/film_loader.py](src/film_loader.py) в [film_loader.py.txt](film_loader.py.txt)
    - ошибки с кодом `E0401` `Unable to import` вызваны отсутствием внешних библиотек в виртуальном окружении т.к. 
    нет файла зависимостей `requirements.txt`
- Для вывода информации по отладке и/или простых сообщений не использовать `print()` для этих целей существует более удобная 
альтернатива - стандартная библиотека `logging`. [Ссылка](https://docs.python.org/3/library/logging.html) на документацию.

#### Рекомендации по [src/app.py](src/app.py)
- Использовать единый стиль импорта в коде  
в файле [src/app.py](src/app.py#L2) строка 2, библиотека клиента Elasticsearch импортируется как
```text
import elasticsearch as ES
```
в файле [src/film_loader.py](src/film_loader.py#L4) импортируется как
```
from elasticsearch import Elasticsearch
```
- В функциях отвечающих за обработку пути `@app.route()` нет необходимости каждый раз создавать 
экземпляр класса Elasticsearch, достаточно создать общий клиент для `app` Flask, так же можно
воспользоватся внешней [библиотекой](https://github.com/CalthorpeAnalytics/flask-elasticsearch) или 
другой аналогичной оберткой.
- [src/app.py](src/app.py#L15) строка 15, называть переменную `validate` так же как и библиотеку (модуль) — плохая идея, 
попробуйте переименовать
- Вызов методов `search()`, `get()` и/или других у клиента Elasticsearch потенциально может вызвать исключение, 
поэтому необходимо использовать блок `try ... except ...` и явно обрабатывать возможные ошибки.
    - [src/app.py](src/app.py#L56) строка 56
    - [src/app.py](src/app.py#L74) строка 74
- В функциях отвечающих за обработку пути `@app.route()` при возврате значений через `return` явно 
указывать код статуса ответа (status) даже если он равен `200` и явно возвращать, где это необходимо, пустой JSON `{}` 
для единообразия используемого `api`.
- Параметры запуска `app` Flask желательно вынести в конфигурационной файл и указать их согласно 
учебного технического задания на разработку т.е  `host='127.0.0.1', port=8000`, запускать приложение на `0.0.0.0` 
(все доступные сетевые интерфейсы - плохой тон).
- Видится необходимым переименовать переменную `defaults` в `default_args`, [src/app.py](src/app.py#L20) строка 20,
и далее для лучшей читаемости кода.
- Если Elasticsearch недоступен ([src/app.py](src/app.py#L71-L72) строка 71-72), логично сразу вернуть 
статус 503 Service Unavailable (сервис недоступен) или другой на усмотрение, и не выполнять выполнять 
дальнейший поиск.

#### Рекомендации по [src/film_loader.py](src/film_loader.py)
- Имена функций `extract()`, `transform()`, `load()` малоинформативны, попробуйте переименовать. 
Например `extract_data_from_sqlite()`, что позволит при чтении кода понять, за что отвечает та или 
иная функция без необходимости детально просматривать сам код.
- Для улучшения кода в проекте рекомендуется использовать аннотацию типов (typing), в том числе и для 
возвращаемых значений. [Ссылка](https://docs.python.org/3/library/typing.html) на документацию.
- При соединении с базой данных, [src/film_loader.py](src/film_loader.py#L13) строка 13,  лучше использовать 
менеджеры контекста, конструкция `with ...` или внешние библиотеки.
- Сложные многострочные SQL запросы, [src/film_loader.py](src/film_loader.py#L19-L33) строка 19-33, как правило выносят 
во внешние файлы и уже оттуда получают строку перед выполнением, или используют шаблонизаторы, 
или конструкторы из внешних библиотек по типу [SQLAlchemy](https://www.sqlalchemy.org)   
- Необходимо соблюдать соглашение PEP 257 (Docstring Conventions), если в функциях есть docstring
- Преобразование "разыменование списка" [src/film_loader.py](src/film_loader.py#L58) строка 58, выгладит 
не понятно, возможно использование: именованных кортежей (namedtuple), обычного словаря (dict), 
класса данных (dataclass) или других структур т.к. `movie_info` это экземпляр 
одной сущности, а не отдельные переменные.
- Возможно, [src/film_loader.py](src/film_loader.py#L71-L72) строка 71-72, следует отказаться от дублирования 
идентификатора `_id`, `id` в документе Elasticsearch и оставить только один.
- Импорт, [src/film_loader.py](src/film_loader.py#L102) строка 102, внутри тела функции нехорошая практика
- Вызов метода `bulk()`, [src/film_loader.py](src/film_loader.py#L116) строка 116, потенциально может вызвать исключение, 
поэтому необходимо использовать блок `try ... except ...` и явно обрабатывать возможные ошибки.

#### Дополнительно:
 - Как правило для каждого проекта используют отдельное виртуальное окружения создаваемое. 
 в командной строке через `python3 -m venv venv`. Каталог `venv` не включают в состав git репозитория.
- В целом обратить внимание на именование переменных, стилистику кода и оформление репозитория.
- В местах где это необходимо использовать обработку исключений.
- Больше писать отладочной информации через logging на соответствующем уровне (ERROR, INFO, DEBUG и т.д.).
- Для хранения настроек использовать конфигурационные файлы и/или переменный окружения, части кода вынести в отдельные файлы.
- В  git репозитории нет тестов для Postman или пояснения по тестированию решения.
