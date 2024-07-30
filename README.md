# AnimeParsers
## Описание
Данный проект нацелен на создание наиболее широкого спектра парсеров на python для различных аниме-плееров в русскоязычном/снг сегменте

## Что есть на данный момент
- [x] Парсер Kodik (автоматическое получение api ключа)
- [x] Асинхронный парсер Kodik
- [x] Парсер AniBoom (на основе animego, не требует api ключей)
- [x] Парсер JutSu (без функции поиска)
- [x] Парсер Shikimori (с возможностью использовать псевдо-api)

## Установка
- Установка без lxml (при импорте классов не забудьте передать параметр use_lxml=False):
    ```commandline
    pip install anime-parsers-ru
    ```
- Установка с lxml:
    ```commandline
    pip install anime-parsers-ru[lxml]
    ```
- Установка с асинхронными библиотеками (без lxml):
    ```commandline
    pip install anime-parsers-ru[async]
    ```

Установка lxml вручную:
```commandline
pip install lxml
```

# Инструкция к парсерам

## Оглавление
- [Kodik инструкция](#kodik-инструкция)
- [AniBoom инструкция](#aniboom-инструкция)
- [JutSu инструкция](#jutsu-инструкция)
- [Shikimori инструкция](#shikimori-инструкция)

## Kodik инструкция

> [!TIP]
> В большинстве случаев в комментариях к функциям описаны шаблоны и возможные значения возвращаемых данных

0. Установите и импортируйте библиотеку
    
    Без lxml:
    ```commandline
    pip install anime-parsers-ru
    ```
    С lxml:
    ```commandline
    pip install anime-parsers-ru[lxml]
    ```
    ```python
    from anime_parsers_ru import KodikParser

    parser = KodikParser(<ваш api ключ>) # Если нет ключа, оставьте пустым
    ```

    __Для асинхронного кода__:
    ```commandline
    pip install anime-parsers-ru[async]
    ```
    (Установка без lxml)
    ```python
    from anime_parsers_ru import KodikParserAsync

    parser = KodikParserAsync(<ваш api ключ>) # Если нет ключа, оставьте пустым
    # Далее перед всеми функциями дополнительно нужно прописывать await
    ```

1. Поиск аниме по названию
    ```python
    parser.search("Наруто") # список словарей
    ```
    Возвращает словарь из аниме и их данных (см. комментарии к функции)

2. Получить информацию об аниме
    ```python
    parser.get_info("z20", "shikimori") # Информация по "Наруто"
    ```
    Возвращает:
    ```json
    {
        "series_count": 220, 
        "translations": [
            {"id": "735", "type": "Озвучка", "name": "2x2 (220 эп.)"}, 
            {"id": "609", "type": "Озвучка", "name": "AniDUB (220 эп.)"}, 
            {"id": "869", "type": "Субтитры", "name": "Субтитры (220 эп.)"}, 
            {"id": "958", "type": "Озвучка", "name": "AniRise (135 эп.)"}, 
            {"id": "2550", "type": "Озвучка", "name": "ANI.OMNIA (8 эп.)"}
        ]
    }
    ```

    - Получить отдельно кол-во серий:
        ```python
        parser.series_count("z20", "shikimori") # число
        ```
    - Получить отдельно переводы:
        ```python
        parser.translations("z20", "shikimori") # список словарей
        ```
3. Прямая ссылка на видеофайл
    ```python
    parser.get_link(
        id="z20", 
        id_type="shikimori", 
        seria_num=1, 
        translation_id="609") # Кортеж
    ```
    Возвращает кортеж: `("//cloud.kodik-storage.com/useruploads/67b6e546-e51d-43d2-bb11-4d8bfbedc2d7/d6f4716bc90bd30694cf09b0062d07a2:2024062705/", 720)`
    
    1. Ссылка
    Пример: `//cloud.kodik-storage.com/useruploads/67b6e546-e51d-43d2-bb11-4d8bfbedc2d7/d6f4716bc90bd30694cf09b0062d07a2:2024062705/`
    К данной ссылке в начале нужно добавить `http:` или `https:`, а в конце качество.mp4 (`720.mp4`) (Обычно доступны следующие варианты качества: `360`, `480`, `720`)
    2. Максимально возможное качество
    Прим: `720` (1280x720)

> [!IMPORTANT]
> В случае, если аниме является фильмом или содержит только одну серию, в параметр `seria_num` указывается значение `0`. В случае если перевод/субтитры неизвестны или нет выбора, в параметр `translation_id` указывается значение `"0"`


4. Получить токен
    ```python
    parser.get_token() # строка
    # Или
    KodikParser.get_token()
    ```
    Использует один из скриптов кодика в котором указан api ключ, поэтому может не работать из-за внесенных изменений

## AniBoom инструкция
0. Установите и импортируйте библиотеку
    ```commandline
    pip install anime-parsers-ru
    ```
    ```python
    from anime_parsers_ru import AniboomParser

    parser = AniboomParser()
    ```

1. Поиск по названию
    1. Быстрый поиск
    ```python
    parser.fast_search("Название аниме")
    ```
    Возвращает список из словарей в виде:
    ```json
    [
            {
                "title": "Название аниме",
                "year": "Год выпуска",
                "other_title": "Другое название(оригинальное название)",
                "type": "Тип аниме (ТВ сериалфильм, ...)",
                "link": "Ссылка на страницу с информацией",
                "animego_id": "id на анимего (по сути в ссылке на страницу с информацией последняя цифра и есть id)"
            },
        ]
    ```
    2. Поиск с дополнительной информацией / Расширенный поиск
    ```python
    parser.search("Название аниме")
    ```
    Возвращает список из словарей:
    ```json
    [
        {
            "title": "Название",
            "other_titles": ["Альтернативное название 1", "..."],
            "status": "Статус аниме (онгоинг, анонс, вышел, ...)",
            "type": "Тип аниме (ТВ сериал, фильм, ...)",
            "genres": ["Жанр1", "Жанр2", "..."],
            "description": "описание",
            "episodes": "если аниме вышло, то количество серий, если еще идет, то 'вышло / всего'",
            "episodes_info": [
                {
                    "num": "Номер эпизода",
                    "title": "Название эпизода",
                    "date": "Даты выхода (предполагаемые если анонс)",
                    "status": "'вышло' или 'анонс' (Имеется в виду вышло в оригинале, не переведено)",
                },
            ],
            "translations": [
                {
                    "name": "Название студии",
                    "translation_id": "id перевода в плеере aniboom"
                },
            ],
            "poster_url": "Ссылка на постер аниме",
            "trailer": "Ссылка на ютуб embed трейлер",
            "screenshots": [
                "Список ссылок на скриншоты"
            ],
            "other_info": {
                // Данная информация может менятся в зависимости от типа или состояния тайтла
                "Возрастные ограничения": "(прим: 16+)",
                "Выпуск": "(прим: с 2 апреля 2024)",
                "Главные герои": ["Список главных героев"],
                "Длительность": "(прим: 23 мин. ~ серия)",
                "Первоисточник": "(прим: Легкая новвела)",
                "Рейтинг MPAA": "(прим: PG-13)",
                "Сезон": "(прим. Весна 2024)",
                "Снят по ранобэ": "название ранобэ (Или так же может быть 'Снят по манге')",
                "Студия": "название студии"
            },
            "link": "Ссылка на страницу с информацией",
            "animego_id": "id на анимего (по сути в ссылке на страницу с информацией последняя цифра и есть id)"
        },
    ]
    ```
2. Данные по эпизодам. Если в аниме 1 эпизод или это фильм, то данных по эпизодам может не быть.
    ```python
    parser.episodes_info('ссылка на страницу аниме на animego.org') # Ссылка доступна из поиска по ключу 'link'
    ```
    Возвращает отсортированный по номеру серии список:
    ```json
    [   
        {
            "num": "Номер эпизода",
            "title": "Название эпизода",
            "date": "Даты выхода (предполагаемые если анонс)",
            "status": "'вышло' или 'анонс' (Имеется в виду вышло в оригинале, не переведено)"
        },
    ]
    ```
3. Данные по аниме (как в полном/расширенном поиске)
    ```python
    parser.anime_info('ссылка на страницу аниме на animego.org') # Ссылка доступна из поиска по ключу 'link'
    ```
    Возвращает словарь:
    ```json
    {
        "title": "Название",
        "other_titles": ["Альтернативное название 1", "..."],
        "status": "Статус аниме (онгоинг, анонс, вышел, ...)",
        "type": "Тип аниме (ТВ сериал, фильм, ...)",
        "genres": ["Жанр1", "Жанр2", "..."],
        "description": "описание",
        "episodes": "если аниме вышло, то количество серий, если еще идет, то 'вышло / всего'",
        "episodes_info": [
            {
                "num": "Номер эпизода",
                "title": "Название эпизода",
                "date": "Даты выхода (предполагаемые если анонс)",
                "status": "'вышло' или 'анонс' (Имеется в виду вышло в оригинале, не переведено)",
            },
        ],
        "translations": [
            {
                "name": "Название студии",
                "translation_id": "id перевода в плеере aniboom"
            },
        ],
        "poster_url": "Ссылка на постер аниме",
        "trailer": "Ссылка на ютуб embed трейлер",
        "screenshots": [
            "Список ссылок на скриншоты"
        ],
        "other_info": {
            // Данная информация может менятся в зависимости от типа или состояния тайтла
            "Возрастные ограничения": "(прим: 16+)",
            "Выпуск": "(прим: с 2 апреля 2024)",
            "Главные герои": ["Список главных героев"],
            "Длительность": "(прим: 23 мин. ~ серия)",
            "Первоисточник": "(прим: Легкая новвела)",
            "Рейтинг MPAA": "(прим: PG-13)",
            "Сезон": "(прим. Весна 2024)",
            "Снят по ранобэ": "название ранобэ (Или так же может быть 'Снят по манге')",
            "Студия": "название студии"
        },
        "link": "Ссылка на страницу с информацией",
        "animego_id": "id на анимего (по сути в ссылке на страницу с информацией последняя цифра и есть id)"
    },
    ```

4. Данные по переводам (которые есть в плеере aniboom)
    ```python
    parser.get_translation_info('animego_id') # Ссылка доступна из поиска по ключу 'animego_id'
    ```
    Возвращает список словарей:
    ```json
    [
        {
            "name": "Название студии озвучки",
            "translation_id": "id перевода в плеере aniboom"
        }
    ]
    ```

5. Получить контент файла mpd (mp4 файл разбитый на чанки) в виде строки. При сохранении данной строки в .mpd файл и при открытии его плеером, котрый поддерживает такой формат (прим: VLC PLayer), можно смотреть серию без рекламы. Обратите внимание, что в данном файле находятся именно ссылки на чанки, а не само видео, поэтому потребуется доступ в интернет. (Вы можете использовать ffmpeg для конвертации этого файла в mp4 формат)
    ```python
    parser.get_mpd_playlist('animego_id', 'episode_num', 'translation_id')
    # animego_id можно найти в результате поиска по ключу 'animego_id' (либо взять последние цифры в ссылке на страницу аниме на animego.org)
    # episode_num - номер вышедшего эпизода (нужно чтобы эпизод вышел именно с выбранной озвучкой)
    # translation_id - id перевода в базе aniboom (Можно найти либо в результате поиска, либо через anime_info, либо через get_translation_info)
    ```
    Возвращает строку - контент mpd файла
    
> [!IMPORTANT]
> В случае, если аниме является фильмом или содержит только одну серию, в параметр `episode_num` указывается значение `0`.

6. Сохранить mpd файл (Дополняет предыдущую функцию get_mpd_playlist)
    ```python
    parser.get_as_file('animego_id', 'episode_num', 'translation_id', 'filename')
    # animego_id можно найти в результате поиска по ключу 'animego_id' (либо взять последние цифры в ссылке на страницу аниме на animego.org)
    # episode_num - номер вышедшего эпизода (нужно чтобы эпизод вышел именно с выбранной озвучкой)
    # translation_id - id перевода в базе aniboom (Можно найти либо в результате поиска, либо через anime_info, либо через get_translation_info)
    # filename - имя файля или путь
    ```
    Сохраняет файл по указанному имени/пути

> [!IMPORTANT]
> В случае, если аниме является фильмом или содержит только одну серию, в параметр `episode_num` указывается значение `0`.

## JutSu инструкция
0. Установите и импортируйте библиотеку
    ```commandline
    pip install anime-parsers-ru
    ```
    ```python
    from anime_parsers_ru import JutsuParser

    parser = JutsuParser()
    ```

1. Данные по аниме (по ссылке на страницу)
    ```python
    parser.get_anime_info("Ссылка на страницу")
    # Пример ссылки: https://jut.su/tondemo-skill/
    # Для аниме: Кулинарные скитания в параллельном мире
    ```
    Возвращает словарь:
    ```json
    {
        "title": "Название аниме",
        "origin_title": "Оригинальное название (транслит японского названия на английском)",
        "age_rating": "Возрастное ограничение",
        "description": "Описание",
        "years": ["Год выхода 1 сезона", "Год выхода 2 сезона"],
        "genres": ["Жанр 1", "Жанр 2"],
        "poster": "Ссылка на картинку (плохое качество)",
        "seasons": [
            [ // 1 сезон будет обязательно, даже если у аниме нет других сезонов
                "ссылка на 1 серию 1 сезона (страница с плеером)",
                "ссылка на 2 серию 1 сезона (страница с плеером)"
            ],
            [ // 2 сезон если есть
                "ссылка на 1 серию 2 сезона (страница с плеером)",
                "ссылка на 2 серию 2 сезона (страница с плеером)"
            ],
        ],
        "seasons_names": [ // Если у аниме только 1 сезон, этот список будет пустым
            "Название 1 сезона", 
            "Название 2 сезона"
        ],
        "films": [ // Если фильмов нет - список пустой
            "Ссылка на фильм 1 (страница с плеером)",
            "Ссылка на фильм 2 (страница с плеером)",
        ]
    }
    ```

2. Получить ссылку на mp4 файл
    ```python
    parser.get_mp4_link('ссылка на страницу с плеером')
    # Пример ссылки: https://jut.su/tondemo-skill/episode-1.html
    # Еще пример ссылки: https://jut.su/ookami-to-koshinryou/season-1/episode-1.html
    ```
    Возвращает словарь:
    ```json
    {
        "360": "ссылка на mp4 файл с качеством 360p",
    }
    ```

> [!IMPORTANT]
> Для разных аниме разное количество доступных качеств плеера. (Например для "Наруто" доступно только 360 и 480, для большинства новых аниме доступно качество до 1080)
> Также jutsu не позволяет выбрать озвучку для аниме.

> [!NOTE]
> Для jutsu нет функции поиска, потому что он использует поиск яндекса по сайту и из-за того что он "умный" он может работать абсолютно непредсказуемо.
> В качестве "поиска" вы можете использовать оригинальное название аниме. Так как ссылка формируется по следующей схеме:
> Название аниме: Волчица и пряности
> Оригинальное название: Ookami to Koushinryou
> Ссылка на страницу: https://jut.su/ookami-to-koshinryou/

## Shikimori инструкция
0. Установите и импортируйте библиотеку
    ```commandline
    pip install anime-parsers-ru
    ```
    ```python
    from anime_parsers_ru import ShikimoriParser

    parser = ShikimoriParser()
    ```

> [!NOTE]
> Шикимори ограничивает частоту запросов на сервер.
> Если шикимори возвращает код ответа 520, парсер вернет exception TooManyRequests.
> Для избежания этой ошибки делайте задержку 1-3 секунды между запросами.

1. Поиск аниме по названию
    ```python
    parser.search('Название аниме')
    ```
    Возвращает список словарей:
    ```json
    [
        {
            "genres": ["Жанр1", "Жанр2"],
            "link": "Ссылка на страницу аниме",
            "original_title": "Оригинальное название (транслит японского названия на английском)",
            "poster": "Ссылка на постер к аниме (плохое качество) если есть, иначе None",
            "shikimori_id": "id шикимори",
            "status": "статус (вышло, онгоинг, анонс)",
            "studio": "студия анимации (если есть, иначе None)",
            "title": "Название",
            "type": "тип аниме (TV сериал, OVA, ONA, ...)",
            "year": "год выхода (если есть, иначе None)"
        }
    ]
    ```

2. Информация об аниме
    ```python
    parser.anime_info('shikimori id')
    # id шикимори можно получить с помощью функции
    # parser.id_by_link
    ```
    Возвращает словарь:
    ```json
    {
        "dates": "Даты выхода",
        "description": "Описание",
        "episode_duration": "Средняя продолжительность серии",
        "episodes": "Количество эпиходов если статус 'вышло' или 'вышедших эпизодов / анонсировано эпизодов' или None (если фильм)",
        "genres": ["Жанр1", "Жанр2"],
        "licensed": "Кто лицензировал в РФ или None",
        "licensed_in_ru": "Название аниме как лицензировано в РФ или None",
        "next_episode": "Дата выхода следующего эпизода или None",
        "original_title": "Оригинальное название",
        "picture": "Ссылка на jpeg постер",
        "premiere_in_ru": "Дата премьеры в РФ или None",
        "rating": "возрастной рейтинг",
        "score": "оценка на шикимори",
        "status": "статус выхода",
        "studio": "студия анимации",
        "themes": ["Тема1", "Тема2"],
        "title": "Название на русском",
        "type": "тип аниме (TV Сериал, Фильм, т.п.)"
    }
    ```

3. Дополнительная информация об аниме (связанные аниме (продолжения, предыстории), авторы, главные персонажи, скриншоты, видео, похожие аниме)
    ```python
    parser.additional_anime_info('Ссылка на страницу шикимори')
    # прим: https://shikimori.one/animes/z20-naruto
    ```
    Возвращает словарь:
    ```json
    {
        "related": [
            {
                "date": "Даты выхода/сезон",
                "name": "Название",
                "picture": "Ссылка на картинку",
                "relation": "тип связи (продолжение, предыстория, адаптация и т.п.)",
                "type": "Тип (TV сериал, OVA, ONA, манга, ранобэ и т.д.)",
                "url": "Ссылка на страницу шикимори"
            }
        ],
        "staff": [
            {
                "name": "Имя человека (на русском)",
                "roles": ["Роль1", "Роль2"],
                "link": "ссылка шикимори на человека"
            }
        ],
        "main_characters": [
            {
                "name": "Имя персонажа",
                "picture": "Картинка (jpeg)"
            }
        ],
        "screenshots": ["Ссылка на скриншот 1", "Ссылка на скриншот 2"],
        "videos": [
            {
                "name": "Название видео",
                "link": "Ссылка на видео (обычно ютуб)"
            }
        ],
        "similar": [
            {
                "name": "Название аниме (похожего)",
                "picture": "Картинка (постер)",
                "link": "Ссылка на шикимори"
            }
        ]
    }
    ```

4. Вспомогательные функции
    - Ссылка на страницу шикимори по id
        ```python
        parser.link_by_id('shikimori_id')
        ```
        Возвращает ссылку
        (id: 53446 результат: https://shikimori.one/animes/53446-tondemo-skill-de-isekai-hourou-meshi)
    - Id по ссылке на шикимори
        ```python
        parser.id_by_link('ссылка на страницу')
        ```
        Возвращает shikimori_id
        (ссылка: https://shikimori.one/animes/53446-tondemo-skill-de-isekai-hourou-meshi id: 53446)

5. Поиск аниме и информации по аниме через псевдо api shikimori
    Данные функции используют предоставленную shikimori тестовую функцию для api. (https://shikimori.one/api/doc/graphql)
    Подробные примеры запросов и ответов вы можете посмотреть в файле [SHIKI_API.md](https://github.com/YaNesyTortiK/AnimeParsers/blob/main/SHIKI_API.md)
    
    - Поиск аниме
        ```python
        parser.deep_search(
            title='Название аниме', 
            search_parameters={'поисковый параметр 1': 'значение поискового параметра 1'},
            return_parameters=['Параметр результата 1', 'параметр результата 2']
        )
        ```
        Возвращает список словарей
    
    - Информация об аниме по id
        ```python
        parser.deep_anime_info(
            shikimori_id='id шикимори',
            return_parameters=['Параметр результата 1', 'параметр результата 2']
        )
        ```
        Возвращает словарь
