# AnimeParsers
## Описание
Данный проект нацелен на создание наиболее широкого спектра парсеров на python для различных аниме-плееров в русскоязычном/снг сегменте

## Что есть на данный момент
- [x] Парсер Kodik (автоматическое получение api ключа)

## Установка
```commandline
pip install anime-parsers-ru
```

# Инструкция к парсерам

## Оглавление
- [Kodik инструкция](#kodik-инструкция)

## Kodik инструкция

> [!TIP]
> В большинстве случаев в комментариях к функциям описаны шаблоны и возможные значения возвращаемых данных

0. Установите и импортируйте библиотеку
    ```commandline
    pip install anime-parsers-ru
    ```
    ```python
    from anime_parsers_ru.parser_kodik import KodikParser

    parser = KodikParser(<ваш api ключ>) # Если нет ключа, оставьте пустым
    ```

1. Поиск аниме по названию
    ```python
    parser.search('Наруто') # список словарей
    ```
    Возвращает словарь из аниме и их данных (см. комментарии к функции)

2. Получить информацию об аниме
    ```python
    parser.get_info('z20', 'shikimori') # Информация по 'Наруто'
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
        parser.series_count('z20', 'shikimori') # число
        ```
    - Получить отдельно переводы:
        ```python
        parser.translations('z20', 'shikimori') # список словарей
        ```
3. Прямая ссылка на видеофайл
    ```python
    parser.get_link(
        id='z20', 
        id_type='shikimori', 
        seria_num=1, 
        translation_id='609') # Кортеж
    ```
    Возвращает кортеж: `('//cloud.kodik-storage.com/useruploads/67b6e546-e51d-43d2-bb11-4d8bfbedc2d7/d6f4716bc90bd30694cf09b0062d07a2:2024062705/', 720)`
    
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