готов
1) Учебный сервис предварительной оценки заявки на заём под ПТС: принимает заявку, считает LTV и возвращает `approve` / `review` / `reject`.
2) Запуск: `make up` (`docker compose up -d --build`); проверки: `make test` (PHPUnit) и `make lint` (`php -l`); ещё есть `make down`, `make ps`, `make logs`, `make install`, `make seed`, `make help`, а в `docker-compose.yml` — запуск PHP-сервера и healthcheck MySQL через `mysqladmin ping`.
3) Решение по заявке считается в папке `backend/src/Domain/`, в `DecisionEngine.php` (используется из `AssessmentService.php`).
модель: OpenAI GPT-5 (Codex, вместо MiniMax M3)
