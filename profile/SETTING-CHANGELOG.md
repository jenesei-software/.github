# 1. Установить standard-version
npm install --save-dev standard-version

# 2. Добавь в package.json скрипт
```json
  "scripts": {
    "release": "standard-version --skip.tag --skip.commit --skip.bump"
  }
```
# 3. Запуск
npm run release
