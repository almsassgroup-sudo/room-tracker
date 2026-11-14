# Room Tracker (онлайн)

Статусы комнат в реальном времени для всех сотрудников. Вход по одному общему паролю.
Хостинг — GitHub Pages, база и вход — Firebase. Ничего устанавливать не нужно, всё делается в браузере.

Файлы репозитория:

| Файл | Зачем |
|---|---|
| `index.html` | Само приложение |
| `firestore.rules` | Правила доступа к базе (вставляются в Firebase) |
| `.github/workflows/deploy.yml` | Автоматическая публикация на GitHub Pages; подставляет настройки Firebase из секретов |

## Часть 1. Firebase (console.firebase.google.com)

1. **Add project** → имя `room-tracker` → Google Analytics выключить → **Create project**.
2. **Build → Authentication → Get started → Sign-in method → Email/Password** → включить первый переключатель (Email link не нужен) → **Save**.
3. Вкладка **Users → Add user**: Email `roomtracker@example.com`, Password — ваш общий пароль (лучше 12+ символов). Почта нужна только как логин, письма не отправляются.
4. **Build → Firestore Database → Create database** → регион европейский (например `eur3`) → **Production mode** → **Enable**.
   Затем вкладка **Rules** → удалить всё → вставить содержимое `firestore.rules` → **Publish**.
5. **Project settings** (шестерёнка) → **General** → **Your apps** → значок `</>` → Nickname `Room Tracker`, галочку Hosting не ставить → **Register app**. Скопируйте из `firebaseConfig`: `apiKey`, `authDomain`, `projectId`, `appId`.

## Часть 2. GitHub (github.com)

6. **New repository** → имя `room-tracker` → **Public** → **Create repository**.
   (Бесплатный GitHub Pages работает только с публичными репозиториями.)
7. **Settings → Pages → Build and deployment → Source: GitHub Actions**.
8. **Settings → Secrets and variables → Actions → New repository secret** — создать 5 секретов:

   | Name | Value |
   |---|---|
   | `FIREBASE_API_KEY` | `apiKey` |
   | `FIREBASE_AUTH_DOMAIN` | `authDomain` |
   | `FIREBASE_PROJECT_ID` | `projectId` |
   | `FIREBASE_APP_ID` | `appId` |
   | `SHARED_EMAIL` | `roomtracker@example.com` (тот же, что в шаге 3) |

9. **Add file → Upload files** → перетащить `index.html`, `firestore.rules`, `README.md` → **Commit changes**.
10. **Add file → Create new file** → в поле имени набрать `.github/workflows/deploy.yml` (слеши создают папки) → вставить содержимое файла `deploy.yml` → **Commit changes**. Это запускает публикацию.
11. Вкладка **Actions** → дождаться зелёной галочки (1–2 минуты). Адрес: `https://ВАШ-ЛОГИН.github.io/room-tracker/`.
12. Firebase → **Authentication → Settings → Authorized domains → Add domain** → `ВАШ-ЛОГИН.github.io`.
13. Откройте адрес, введите общий пароль. Проверьте на двух устройствах: изменение статуса должно появляться у обоих сразу.

## Смена пароля
Firebase → Authentication → Users → удалить пользователя → **Add user** с тем же email и новым паролем.

## Если что-то не работает
- Красный крестик в Actions «Secret … is not set» — секрет не создан или имя написано с ошибкой.
- «Firebase settings not found» — страница открыта как локальный файл; открывайте адрес GitHub Pages.
- «Email/Password sign-in is not enabled» — не выполнен шаг 2.
- «Sync error: permission-denied» — email в `firestore.rules` не совпадает с email пользователя из шага 3.
- «Wrong password» при верном пароле — проверьте, что `SHARED_EMAIL` совпадает с email пользователя в Firebase, затем запустите Actions → Deploy → Run workflow.

## О безопасности
Настройки Firebase для веба не являются тайной: браузеру они нужны, поэтому попадают на опубликованный сайт. Секреты GitHub лишь не дают им лежать в коде репозитория. Реальная защита — пароль и правила `firestore.rules`: без входа под общим аккаунтом база недоступна.
