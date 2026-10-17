# Unit-тесты с AI-агентом (Hexlet, 2026)

[English](README.md) | [Русский](README.ru.md)

Учебный пример Hexlet: генерация unit-тестов Python с помощью AI-агента в GitHub Actions. Разработчик открывает pull request, агент проверяет покрытие и добавляет тесты в ту же ветку, а ответственный за проект проверяет результат.

Небольшой объем проекта позволяет изучать исходники, тесты и изменения агента вместе.

## Как работает пример

```text
Feature branch -> Pull request to main -> GitHub Actions -> AI agent
                                                       -> Test commit on the PR branch
                                                       -> Maintainer review
```

1. Реализуйте функцию в ветке `feature/*`.
2. Откройте pull request в `main`.
3. Workflow `AI Test Agent` запустится при открытии или обновлении PR.
4. Агент прочитает diff, проверит покрытие и напишет тесты для непокрытых функций.
5. Он запустит тесты, затем создаст коммит и отправит изменения тестов в ветку PR.
6. Ответственный за проект проверит код и сгенерированные тесты перед принятием или отклонением PR.

## Структура проекта

| Путь | Содержимое |
| --- | --- |
| [.github/workflows/ai-test-agent.yml](.github/workflows/ai-test-agent.yml) | Workflow и промпт агента |
| [features/add-data-processor.md](features/add-data-processor.md) | Задание по обработке данных и критерии приемки |
| [features/add-geometry.md](features/add-geometry.md) | Задание по геометрии и критерии приемки |
| [src](src) | Модули Python для учебного примера |
| [tests](tests) | Unit-тесты модулей |
| [pyproject.toml](pyproject.toml) | Требования Python, зависимости разработки и настройки pytest |

## Модули и упражнения с тестами

Исходное упражнение предлагает агенту найти недостающие тесты для перечисленных ниже функций. Сейчас в репозитории есть тестовые файлы для всех четырех модулей. Следующий сценарий выбирайте по отчету покрытия: наличие тестового файла само по себе не показывает, проверены ли все пути выполнения.

| Модуль | Функции | Тестовый файл |
| --- | --- | --- |
| [calculator.py](src/calculator.py) | `add`, `subtract`, `multiply`, `divide`, `factorial`, `is_prime`, `gcd` | [test_calculator.py](tests/test_calculator.py) |
| [string_utils.py](src/string_utils.py) | `reverse`, `capitalize_words`, `is_palindrome`, `count_vowels` | [test_string_utils.py](tests/test_string_utils.py) |
| [data_processor.py](src/data_processor.py) | `filter_by_key`, `sort_by_key`, `aggregate_by_key`, `merge_records` | [test_data_processor.py](tests/test_data_processor.py) |
| [geometry.py](src/geometry.py) | `circle_area`, `circle_circumference`, `rectangle_area`, `rectangle_perimeter`, `triangle_area` | [test_geometry.py](tests/test_geometry.py) |

Задание по обработке данных охватывает списки словарей, фильтрацию, сортировку, подсчет значений и объединение последовательных записей. Критерии приемки включают пустые входные данные и случаи с отсутствующими ключами и `None`. Геометрическое задание охватывает круги, прямоугольники и треугольники: `ValueError` для отрицательных размеров, нулевые размеры и результат типа float. Подробные условия сохранены в `features/`.

## Локальный запуск

Нужен Python 3.10 или новее. В CI используется Python 3.12.

```bash
git clone https://github.com/ivan-zamula/ai-agent-unit-tests-2026-hexlet.git
cd ai-agent-unit-tests-2026-hexlet
python -m venv .venv
source .venv/bin/activate
pip install -e ".[dev]"
```

Запустите тесты и посмотрите покрытие:

```bash
pytest tests/ -v
pytest tests/ --cov=src --cov-report=term-missing
```

## GitHub Actions и агент

Workflow использует GitHub environment `demo`, Python 3.12 и Node.js/npm. Задание запрашивает разрешения `contents: write` и `pull-requests: write`, чтобы агент мог отправлять коммиты с тестами в ветку PR. В текущем варианте используется OpenCode:

```bash
npm install -g opencode-ai
opencode run --model opencode/big-pickle "..."
```

Полный промпт находится в [ai-test-agent.yml](.github/workflows/ai-test-agent.yml). Он задает агенту следующие шаги:

1. Сравнить PR с `main`.
2. Запустить pytest с покрытием и найти непокрытые функции.
3. Прочитать исходники и тесты, чтобы следовать стилю проекта.
4. Добавить обычные, граничные и ошибочные сценарии.
5. Запустить тесты и исправить сгенерированные тесты, которые не прошли.
6. Создать коммит от `ai-test-agent[bot]` и отправить его в ветку PR.

Workflow также формирует сводку GitHub Actions со ссылками на PR и запуск.

## Адаптация примера

- Для другого провайдера или модели измените команду OpenCode и ее окружение. Нужные ключи, например `ANTHROPIC_API_KEY`, `OPENAI_API_KEY` или `GEMINI_API_KEY`, храните в GitHub Secrets и передавайте в шаг агента. Само добавление секрета не настраивает этот шаг.
- Добавьте в промпт правила проекта, например использование `unittest` или моков внешних API.
- Для замены агента измените и установку, и команду вызова.
- Docker-образ с установленными агентом и зависимостями может сократить подготовку каждого запуска.

Цель примера — изучить добавление AI-тестов в CI/CD, сохранив проверку изменений за ответственным за проект.
