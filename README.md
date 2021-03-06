# ponyfiction.org

Данный репозиторий содержит статические файлы, шаблоны и конфигурацию сайта
[ponyfiction.org](https://ponyfiction.org/), работающего на движке
[mini_fiction](https://github.com/andreymal/mini_fiction).


## Установка

* Устанавливаем системные зависимости:

  - Python 3.6 или выше для бэкенда
  - NodeJS и npm для сборки фронтенда
  - Sphinx или Manticore для поиска
  - MySQL/MariaDB для базы данных (опционально, по умолчанию используется sqlite3)

* Скачиваем репозиторий

```
git clone https://github.com/andreymal/ponyfiction.org
cd ponyfiction.org
```

* Далее устанавливаем сам mini_fiction. Можно как установить готовый whl-пакет
  (если он есть), так и склонировать git-репозиторий и установиться из него.
  Рекомендуется использовать виртуальное окружение.

  Для боевого окружения не забудьте установить дополнительные зависимости
  из файла `mini_fiction/optional-requirements.txt` (в dev-окружении команда
  `make develop` установит их автоматически).

  Команда `make develop` также автоматически установит Webpack и соберёт
  бандлы для фронтенда.

```
virtualenv env
. env/bin/activate

git clone https://github.com/andreymal/mini_fiction
cd mini_fiction
make develop
cd ..  # назад в каталог ponyfiction.org
```

* ponyfiction.org добавляет свои файлы для фронтенда, их тоже нужно собрать
  с помощью Webpack:

```
npm install
npm run-script build
```

* Фронтенд будет собран в папку `ponyfiction`. По умолчанию mini_fiction
  настроен на раздачу дополнительной статики из каталога `localstatic`, так что
  создаём его и кладём туда символическую ссылку на собранную статику —
  mini_fiction автоматически найдёт её и подключит к сайту.

```
mkdir localstatic
cd localstatic
ln -s ../ponyfiction
cd ..
```

* Flask по умолчанию работает в режиме production. Если вы настраиваете
  окружение для разработки, создайте файл `.env` и пропишите в нём нужный
  `FLASK_ENV`:

```
FLASK_ENV=development
```

* Настройки сайта автоматически считываются из класса `Local` в файле
  `local_settings.py`, если он есть. Можно создать его, взяв за основу
  `local_settings.example.py`:

```
cp local_settings.example.py local_settings.py
```

(Возможно, понадобится прописать переменную окружения `PYTHONPATH=.` в случае,
если `local_settings.py` не подхватится сам по себе.)

В файле `local_settings.py` предлагается настроить `SECRET_KEY`, подключение
к MySQL, отправку почты и прочее.

* Загружаем и распаковываем актуальный дамп некоторых объектов базы данных
  (жанры, персонажи, картинки в шапке и т.п.) отсюда:

  https://ponyfiction.org/dump/

  Помещаем `media` туда, где собственно должен располагаться каталог `media`
  (если вы изменили путь в настройках), а дамп базы загружаем следующей
  командой (после чего каталог `dump` можно удалить):

```
mini_fiction loaddb dump
```

* Если скачать дамп нет возможности или не хочется, то запускаем
  `mini_fiction seed` и всё остальное заполняем позже через админку.

* Проверяем работоспособность настроек:

```
mini_fiction status
```

* Если нужно, создаём суперпользователя:

```
mini_fiction createsuperuser
```

* Теперь можно запустить сервер:

```
mini_fiction run
```

* После запуска сайт станет доступен по адресу `http://localhost:5000/`

* Если вы хотите сгрузить каталог `static` куда-то отдельно для, например,
  раздачи через nginx, можно прописать в настройках путь к нужному вам
  каталогу, например: `STATIC_ROOT = os.path.join(os.getcwd(), 'static')` —
  и складировать туда статику командой `mini_fiction collectstatic`. Но тогда
  нужно не забывать повторять эту команду при каждом обновлении mini_fiction.

* Запуск Celery для обработки различных задач (в этом примере worker и beat
  в одном процессе):

```
celery -A mini_fiction worker -B --loglevel=INFO
```

* Запуск Sphinx или Manticore для поиска рассказов и глав (тоже из-под
  virtualenv, если он есть):

```
searchd -c sphinxconf.sh --nodetach
```

Подробнее про настройку и развёртывание читайте в файле
`mini_fiction/INSTALL.md`.
