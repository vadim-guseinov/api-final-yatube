<div align="center">

# 📝 Yatube API

**REST API социальной сети: посты, группы, комментарии и подписки на авторов с JWT-аутентификацией**

![Python](https://img.shields.io/badge/Python-3.12-3776AB?logo=python&logoColor=white)
![Django](https://img.shields.io/badge/Django-5.1-092E20?logo=django&logoColor=white)
![DRF](https://img.shields.io/badge/DRF-3.15-A30000?logo=django&logoColor=white)
![JWT](https://img.shields.io/badge/Auth-JWT-000000?logo=jsonwebtokens&logoColor=white)
![Tests](https://img.shields.io/badge/pytest-55%2F55_passed-2ea44f?logo=pytest&logoColor=white)

</div>

## О проекте

Yatube API — бэкенд для блог-платформы, к которому может подключиться любой клиент: веб-приложение, мобильное приложение или бот.
Пользователи публикуют посты (в том числе с картинками и в тематических группах), комментируют записи и подписываются на интересных авторов.

## Возможности

- **JWT-аутентификация** (Simple JWT): получение, обновление и проверка access/refresh-токенов.
- **Права доступа**: анонимный пользователь только читает; изменять и удалять пост или комментарий может только его автор (собственный permission `IsAuthorOrReadOnly`).
- **Посты** — полный CRUD, опциональная пагинация `limit/offset`.
- **Комментарии** — вложенный ресурс `/posts/{id}/comments/`.
- **Группы** — только чтение.
- **Подписки** — список своих подписок с поиском по username; валидация запрещает подписку на самого себя и повторную подписку.
- Интерактивная документация ReDoc по адресу `/redoc/`.

## Стек

Python 3.12 · Django 5.1 · Django REST Framework 3.15 · Simple JWT · Djoser · django-filter · SQLite · pytest

## Что сделано

- Модели `Post`, `Group`, `Comment`, `Follow`, сериализаторы с валидацией на уровне API.
- ViewSet-ы и роутер DRF, вложенные маршруты для комментариев, `select_related` для устранения N+1 при выдаче комментариев и подписок.
- Настроены JWT-аутентификация, глобальные права `IsAuthenticatedOrReadOnly` и фильтрация.
- API проходит **55 из 55** автотестов (pytest) и Postman-коллекцию из `postman_collection/`.

## Запуск локально

**Требования:** Python 3.10+ (проверено на 3.12).

```bash
git clone https://github.com/JuliaDJ1/api-final-yatube.git
cd api-final-yatube

python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt

cd yatube_api
python manage.py migrate
python manage.py createsuperuser
python manage.py runserver
```

Документация: http://127.0.0.1:8000/redoc/

### Тесты

```bash
pytest          # из корня репозитория
```

## Эндпоинты

| Метод | URL | Описание |
|---|---|---|
| POST | `/api/v1/jwt/create/` | получить пару JWT-токенов |
| POST | `/api/v1/jwt/refresh/` | обновить access-токен |
| POST | `/api/v1/jwt/verify/` | проверить токен |
| GET, POST | `/api/v1/posts/` | список постов / создать пост |
| GET, PUT, PATCH, DELETE | `/api/v1/posts/{id}/` | пост |
| GET, POST | `/api/v1/posts/{post_id}/comments/` | комментарии к посту |
| GET, PUT, PATCH, DELETE | `/api/v1/posts/{post_id}/comments/{id}/` | комментарий |
| GET | `/api/v1/groups/`, `/api/v1/groups/{id}/` | группы |
| GET, POST | `/api/v1/follow/` | мои подписки / подписаться |

## Примеры запросов

Получить токен:

```bash
curl -X POST http://127.0.0.1:8000/api/v1/jwt/create/ \
  -H "Content-Type: application/json" \
  -d '{"username": "user", "password": "strongpassword"}'
# {"refresh": "<refresh>", "access": "<access>"}
```

Создать пост:

```bash
curl -X POST http://127.0.0.1:8000/api/v1/posts/ \
  -H "Authorization: Bearer <access>" \
  -H "Content-Type: application/json" \
  -d '{"text": "Первый пост через API", "group": 1}'
```

Ответ:

```json
{
  "id": 1,
  "author": "user",
  "text": "Первый пост через API",
  "pub_date": "2026-03-01T12:00:00Z",
  "image": null,
  "group": 1
}
```

Посты с пагинацией:

```bash
curl "http://127.0.0.1:8000/api/v1/posts/?limit=10&offset=20"
```

Подписаться на автора и найти подписку:

```bash
curl -X POST http://127.0.0.1:8000/api/v1/follow/ \
  -H "Authorization: Bearer <access>" \
  -H "Content-Type: application/json" \
  -d '{"following": "leo"}'

curl "http://127.0.0.1:8000/api/v1/follow/?search=leo" -H "Authorization: Bearer <access>"
```

## Автор

**Вадим Гусейнов** — Python-разработчик (Backend) · [Telegram](https://t.me/eviliing)
