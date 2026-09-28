# Независимое ревью ДЗ.1 — MILEAGE

Проверены `intent_MILEAGE.md`, `spec_MILEAGE.md`, `plan_MILEAGE.md`, оба указанных unit-теста и реализация в `rules.php`, `AppFactory.php`, `AssessmentService.php`. Проверка выполнена по исходникам; тестовый набор в рамках этого ревью не запускался.

## Покрытие требований

| Требование | Тесты | Вердикт |
|---|---|---|
| REQ-MILEAGE-01: пробег до и включая 400000 км не меняет `approve` | `AssessmentServiceTest::testKeepsApproveImmediatelyBelowMileageThreshold` (399999), `testKeepsApproveAtMileageThreshold` (400000) | Покрыто; решение и одобренный лимит проверяются. |
| REQ-MILEAGE-02: выше 400000 км `approve` становится `review` | `AssessmentServiceTest::testSendsApproveToReviewImmediatelyAboveMileageThreshold` (400001) | Покрыто; проверены `review` и нулевой лимит. |
| REQ-MILEAGE-03: более строгое решение сохраняется | `AssessmentServiceTest::testHighMileageDoesNotSoftenReviewDecision`, `testHighMileageDoesNotSoftenRejectDecision` (оба при 400001) | Покрыто для `review` и `reject`; обе проверки также подтверждают нулевой лимит. |
| REQ-MILEAGE-04: отсутствующий пробег — ошибка валидации | `ApplicationValidatorTest::testRejectsApplicationWithoutMileage` удаляет поле и проверяет ошибку `mileage` | Покрыто на границе валидатора. Ошибка прерывает `AssessmentService::assess()` до расчёта решения. |
| REQ-MILEAGE-05: действующий максимум 500000 км сохраняется отдельно от бизнес-порога | `ApplicationValidatorTest::testAcceptsMileageAtValidationMaximum` (500000), `testRejectsMileageAboveValidationMaximum` (500001); `AssessmentServiceTest::testAcceptsValidationMaximumAndSendsApproveToReview` | Покрыто: проверены приём 500000, его оценка как `review` и ошибка для 500001. |

## Замечания

- Тесты не ослаблены: существующие сценарии LTV для `approve`, `review`, `reject`, проверка всех ошибок и проверка максимума валидации сохранены; добавлены отдельные проверки пробега с конкретными значениями и результатами.
- Лишних требований относительно intent/spec не обнаружено. Проверка нулевого `approved_limit` при `review` закрепляет уже существующее поведение сервиса, как указано в критериях приёмки.
- Реализация сохраняет разделение порогов: `review_mileage_km = 400000` задаёт бизнес-решение, `max_mileage_km = 500000` — допустимость входа. `AppFactory` передаёт бизнес-порог в сервис; сервис меняет только `approve` при `mileage > reviewMileageKm`, а лимит выводит из итогового решения.
- Проверка отсутствующего поля находится в `ApplicationValidatorTest`, а не в тесте сервиса; этого достаточно для заявленного контракта: валидатор выбрасывает `ValidationException`, поэтому вычисление решения не продолжается.
- Открытый вопрос о пересмотре максимума 500000 км остаётся за пределами задачи, как предписано intent.

## Итог

**Зачтено.** Все пять REQ имеют соответствующие тесты, согласованные с критериями приёмки. Границы 399999/400000/400001, отсутствие пробега, приоритет `review`/`reject` и значения 500000/500001 отражены в проверках. По прочитанным материалам замечаний, блокирующих приёмку, нет.
