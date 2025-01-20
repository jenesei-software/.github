## Локальная установка и настройка `@jenesei-software/name-of-package`

Для локальной работы с пакетом `@jenesei-software/name-of-package` необходимо настроить файл конфигурации `.npmrc`, чтобы обеспечить доступ к приватному репозиторию GitHub Packages.

1. **Создайте или откройте файл `.npmrc`:**
   - В корневой директории вашего проекта создайте файл с именем `.npmrc`, если его еще нет.

2. **Добавьте настройки для доступа к GitHub Packages:**
   - Вставьте следующие строки в ваш файл `.npmrc`:
   ```plaintext
   //npm.pkg.github.com/:_authToken=your_token
   @jenesei-software:registry=https://npm.pkg.github.com/
   ```
3. **Замените `your_token` на ваш токен доступа к GitHub. Этот токен должен иметь права на доступ к приватным репозиториям.**

## Добавление доступа к пакету `@jenesei-software/name-of-package`

Чтобы использовать пакет `@jenesei-software/name-of-package` в GitHub Actions, необходимо предоставить вашему проекту доступ к GitHub Actions для работы с приватными пакетами. 

### Шаги по добавлению доступа

1. **Перейдите в настройки пакета:**
   - Откройте браузер и перейдите по следующему адресу:
     ```
     https://github.com/orgs/jenesei-software/packages/npm/name-of-package/settings
     ```

2. **Настройка доступа к GitHub Actions:**
   - На странице настроек найдите раздел **Manage Actions access**.
   - В этом разделе вы увидите список проектов, которым можно предоставить доступ. Найдите ваш проект и добавьте его в список.
