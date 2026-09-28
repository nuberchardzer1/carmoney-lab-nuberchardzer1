# Собственные агенты

## Роли

- `planner` (`mode: primary`) читает проект и составляет планы. Ему запрещены bash и любые правки, кроме файлов в `docs/plan/**`.
- `scout` (`mode: subagent`) только ищет места в коде и возвращает путь, строку и краткое описание. Ему запрещены bash и любое редактирование.

## Результат вызова scout

Запрос: `@scout найди все места, где читается пробег (mileage)`.

- `frontend/app.js:8` — `mileage` входит в список числовых полей формы.
- `frontend/index.html:30–31` — форма содержит обязательный числовой ввод пробега.
- `backend/src/Domain/ApplicationValidator.php:43–45` — пробег читается из payload и проверяется по диапазону от `0` до `max_mileage_km`.
- `backend/src/Domain/ApplicationValidator.php:78` — нормализованный пробег возвращается дальше по цепочке.
- `backend/config/rules.php:23` — текущий верхний предел `max_mileage_km` равен `500000`.
- `backend/src/Repository/ApplicationRepository.php:38–45` — пробег сохраняется в `vehicles.mileage_km`.
- `backend/src/Repository/ApplicationRepository.php:68` — пробег читается из БД вместе с заявкой.
- `db/schema.sql:22` и `db/seed.sql:31` — поле пробега обязательно в схеме и присутствует в синтетических данных.
- `tests/Unit/ApplicationValidatorTest.php:34` и `tests/Unit/AssessmentServiceTest.php:38` — существующие тестовые заявки передают пробег, но граничных тестов пока нет.

Scout также нашёл упоминания пробега в README и документации; для плана использованы только реальные точки ввода, проверки, хранения и тестирования кода.
