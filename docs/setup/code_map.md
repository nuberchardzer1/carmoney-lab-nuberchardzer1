# Карта кода: решение по заявке и правило пробега

## Цепочка «файл → функция → порядок»

1. `backend/src/Http/ApplicationController.php` → `create()` или `ltv()` получает payload и вызывает `AssessmentService::assess()`.
2. `backend/src/Domain/AssessmentService.php` → `assess()` управляет всей цепочкой.
3. `backend/src/Domain/ApplicationValidator.php` → `validate()` нормализует поля или бросает `ValidationException`; пороги берёт из `backend/config/rules.php`.
4. `backend/src/Domain/LtvCalculator.php` → `calculate(requested_amount, market_value)` считает LTV в процентах.
5. `backend/src/Domain/DecisionEngine.php` → `decide(ltv)` сравнивает LTV с `ltv.approve_max` и `ltv.review_max` и возвращает `approve`, `review` или `reject`.
6. `AssessmentService::assess()` → для `approve` ставит `approved_limit` равным запрошенной сумме, для остальных решений — `0`.

## Точка вставки правила 400 000 км

Логичная точка — `AssessmentService::assess()` сразу после решения по LTV и до формирования ответа. Реальный контекс сейчас:

```php
$input = $this->validator->validate($payload);
$ltv = $this->ltvCalculator->calculate($input['requested_amount'], $input['market_value']);
$decision = $this->decisionEngine->decide($ltv);

return [
```

Здесь уже есть нормализованный `$input['mileage']` и решение по LTV, поэтому можно применить правило «если пробег > 400 000, решение `review`». Не хватает самого порога 400 000 в `rules.php`; также в тексте требования не сказано, должно ли правило пробега смягчать уже полученный `reject` до `review`.

## Что уже проверяется про пробег

`ApplicationValidator::validate()` требует, чтобы пробег был от `0` до `vehicle.max_mileage_km`. Сейчас `backend/config/rules.php` задаёт `max_mileage_km = 500000`; большее значение даёт ошибку валидации, а не `review`. Отдельных unit-тестов на границы пробега сейчас нет.
