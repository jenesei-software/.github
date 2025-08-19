# 1. Установить standard-version
npm install -g conventional-changelog-cli

# 2. Добавь в package.json скрипт
```json
  "scripts": {
    "changelog": "conventional-changelog -p eslint -i CHANGELOG.md -s -r 0"
  }
```
# 3. Запуск
npm run changelog
