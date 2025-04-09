# Demo CI/CD Project

Это минимальный проект для демонстрации навыков работы с инфраструктурой CI/CD, Docker, GitLab и GitLab Runner. Проект включает:
- Приложение на Python
- Набор unit-тестов для проверки приложения
- Конфигурацию GitLab CI/CD в файле `.gitlab-ci.yml`
- Docker Compose файл для деплоя staging-среды

## Структура проекта
-README.md
-.gitlab-ci.yml
-docker-compose.yml
-app
    [
        -app.py
        -tests
        [
            test_app.py
        ]
    ]

## Описание пайплайна CI/CD

Пайплайн определён в файле `.gitlab-ci.yml` и состоит из следующих этапов:

1. Build 
   - Выполняется сборка приложения (в данном случае – простая команда `echo "Сборка приложения..."`).
   - Этап можно расширить командами для сборки образов, установки зависимостей и т.д.

2. Test
   - Выполняется запуск unit-тестов с помощью команды:
     python -m unittest discover -v app/tests
   - Тесты находятся в каталоге `app/tests` и проверяют, что функция `hello()` в `app/app.py` возвращает ожидаемое значение.

3. Lint
   - Используется flake8 для проверки соответствия кода стандарту PEP8.
   - Команда запуска:
     ```bash
     pip install flake8 && flake8 app/
     ```

4. Deploy
   - Этап деплоя запускает staging-среду с использованием Docker Compose.
   - Docker Compose файл (`docker-compose.yml`) определяет сервис `app`, который базируется на образе `python:3.9` и поднимает простой HTTP-сервер, с помощью `python -m http.server 80`).
   - Для запуска деплоя используется команда:
     ```bash
     docker-compose up -d
     ```
   -В нашем примере в пайплайне используется подход с пробросом сокета в `config.toml`.
   если вдруг будет ругаться, необходимо проверить `config.toml` в контейнере 
   значение `privileged` = true, значение `volumes = ["/var/run/docker.sock:/var/run/docker.sock", "/cache"]`

5. **Notify** 
   - Этап уведомления может отправлять сообщения в телегу о результате выполнения пайплайна.
   - @IOI_Ibot