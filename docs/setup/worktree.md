# Worktree и вторая сессия

## `git worktree list`

```text
C:/Users/Simon/carmoney-lab                             70f81a7 [d1/1.2.1-1.2.3-nuberchardzer1]
C:/Users/Simon/carmoney-lab/.kilo/worktrees/unit-tests  70f81a7 [d1/1.2.3-agent-nuberchardzer1]
```

## Ответ агента из второй сессии

- `ApplicationValidatorTest.php` — проверяет приём корректной заявки, нормализацию VIN и ошибки года, суммы и нескольких полей.
- `AssessmentServiceTest.php` — проверяет LTV, `approve` / `review` / `reject`, лимит и возраст авто на уровне всей оценки.
- `DecisionEngineTest.php` — проверяет выбор решения по разным значениям и границам LTV.
- `LtvCalculatorTest.php` — проверяет расчёт LTV и ошибки для нулевой стоимости и неположительной суммы.
- `VinValidatorTest.php` — проверяет длину, регистр, алфавит и запрещённые символы VIN.

Текущая папка второй сессии: `C:/Users/Simon/carmoney-lab/.kilo/worktrees/unit-tests`.
Текущая ветка: `d1/1.2.3-agent-nuberchardzer1`.

## ★ Почему агентам нельзя делить одну папку и ветку

Два агента могут одновременно перезаписать один файл или индекс, потеряв изменения друг друга.
Отдельные worktree и ветки изолируют рабочие копии и делают изменения сравнимыми через Git.
