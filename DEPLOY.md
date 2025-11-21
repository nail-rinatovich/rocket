# Инструкции по развертыванию на Render.com

Этот проект настроен для развертывания на Render.com.

## Подготовка

1. Убедитесь, что все файлы закоммичены в Git:
   ```bash
   git add .
   git commit -m "Подготовка к развертыванию на Render"
   ```

2. Создайте репозиторий на GitHub (если еще не создан) и отправьте код:
   ```bash
   git remote add origin <ваш-repo-url>
   git push -u origin main
   ```

## Развертывание на Render.com

### Вариант 1: Использование render.yaml (рекомендуется)

1. Перейдите на [Render.com](https://render.com) и войдите в свой аккаунт
2. Нажмите "New" → "Blueprint"
3. Подключите ваш GitHub репозиторий
4. Render автоматически обнаружит `render.yaml` и использует его конфигурацию

### Вариант 2: Ручная настройка

1. Перейдите на [Render.com](https://render.com) и войдите в свой аккаунт
2. Нажмите "New" → "Web Service"
3. Подключите ваш GitHub репозиторий
4. Заполните настройки:
   - **Name**: django-project (или любое другое имя)
   - **Environment**: Python 3
   - **Build Command**: `./build.sh`
   - **Start Command**: `gunicorn django_project.wsgi:application`
   - **Plan**: Free

5. Настройте переменные окружения в разделе "Environment Variables":
   - `SECRET_KEY`: Сгенерируйте новый секретный ключ (можно использовать `python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"`)
   - `DEBUG`: `False`
   - `ALLOWED_HOSTS`: `your-app-name.onrender.com` (замените на ваш реальный домен)

6. Нажмите "Create Web Service"

## Настройка базы данных PostgreSQL (опционально)

Если вы хотите использовать PostgreSQL вместо SQLite:

1. В Render Dashboard нажмите "New" → "PostgreSQL"
2. Выберите план (Free для начала)
3. Создайте базу данных
4. Скопируйте **Internal Database URL** (или External Database URL для доступа извне)
5. В настройках вашего Web Service добавьте переменную окружения:
   - **Key**: `DATABASE_URL`
   - **Value**: Вставьте скопированный URL

Render автоматически предоставит переменную `DATABASE_URL`, поэтому вам не нужно вручную добавлять ее, если вы создали базу данных через интерфейс Render.

## Локальная разработка

Для локальной разработки создайте файл `.env` в корне проекта:

```
SECRET_KEY=your-secret-key-here
DEBUG=True
ALLOWED_HOSTS=localhost,127.0.0.1
```

## Полезные команды

- Проверить логи: В Render Dashboard → Logs
- Перезапустить сервис: Render Dashboard → Manual Deploy → Clear build cache & deploy
- Выполнить миграции вручную: Render Dashboard → Shell → `python manage.py migrate`

## Структура файлов для развертывания

- `requirements.txt` - зависимости Python
- `Procfile` - команда запуска приложения
- `build.sh` - скрипт сборки (миграции и сбор статики)
- `render.yaml` - конфигурация для автоматического развертывания
- `.gitignore` - файлы, которые не должны попадать в репозиторий

## Примечания

- На бесплатном плане Render приложение "засыпает" после 15 минут бездействия
- Первый запуск может занять несколько минут
- Убедитесь, что `DEBUG=False` в production
- Всегда используйте безопасный `SECRET_KEY` в production

