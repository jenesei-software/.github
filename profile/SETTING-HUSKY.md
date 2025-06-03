# 1. Установить husky, commitlint и конфиг conventional
npm install --save-dev husky @commitlint/config-conventional @commitlint/cli

# 2. Инициализировать husky (создаст папку .husky)
npx husky install

# 3. Настроить git использовать .husky/ как папку для хуков
git config core.hooksPath .husky

# 4. Создать commit-msg хук вручную (содержимое ниже)
@"
#!/bin/sh
npx --no -- commitlint --edit \"$1\"
"@ | Out-File -Encoding utf8 .husky/commit-msg

# 5. Сделать хук исполняемым (добавить +x)
git update-index --add --chmod=+x .husky/commit-msg

# 6. Создать валидный конфиг commitlint (json)
@"
{
  \"extends\": [\"@commitlint/config-conventional\"]
}
"@ | Out-File -Encoding utf8 .commitlintrc.json