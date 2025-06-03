# 1. Установить Commitizen и адаптер conventional-changelog
npm install --save-dev commitizen cz-conventional-changelog

# 2. Добавить в package.json секцию config для Commitizen
```json
"config": {
  "commitizen": {
    "path": "cz-conventional-changelog"
  }
}
```
# 3. Запуск
npx cz
