## Описание глобальных Workflows

### Frontend Build ([frontend_build.yml](../.github/workflows/frontend_build.yml))

- Автоматическая сборка фронтенд-проекта.  
- Выполняет клонирование репозитория, установку зависимостей, сборку проекта для текущей ветки и деплой в отдельную ветку (`build_<branch>`).  
- Выбор `npm`, `yarn`, `bun`, по умолчанию `npm`.
- Поддерживает автоматическое обновление версии (major, minor, patch) и работу с приватными пакетами GitHub Packages.

- Пример использования:
  ```yml
    name: Frontend Build Wrapper

    on:
      workflow_dispatch:
        inputs:
          version_type:
            description: 'Version bump'
            required: false
            default: 'none'
            type: choice
            options:
              - none
              - major
              - minor
              - patch

    jobs:
      call-publisher:
        uses: jenesei-software/.github/.github/workflows/frontend_build.yml@main
        with:
          version_type: ${{ inputs.version_type }}
        secrets:
          ACCESS_GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN  }}
  ```
---

### Library Publish ([library_publish.yml](../.github/workflows/library_publish.yml))

- Публикация npm-библиотеки в выбранный реестр: GitHub Packages, npm или оба сразу.  
- Выполняет автоматический bump версии, пушит теги и коммиты, проверяет наличие версии в реестрах и публикует пакет только если версия ещё не опубликована.  
- Поддерживает работу с приватными пакетами и опциональный токен для публикации в npm.
- Выбор `npm`, `yarn`, `bun`, по умолчанию `npm`.
- Пример использования:
  ```yml
  name: Library Publish Wrapper

  on:
    workflow_dispatch:
      inputs:
        registry_type:
          description: 'Registry type'
          required: true
          default: 'npm'
          type: choice
          options:
            - npm
            - github
            - all
        version_type:
          description: 'Version bump'
          required: true
          default: 'patch'
          type: choice
          options:
            - major
            - minor
            - patch

  jobs:
    call-publisher:
      uses: jenesei-software/.github/.github/workflows/library_publish.yml@main
      with:
        registry_type: ${{ inputs.registry_type }}
        version_type: ${{ inputs.version_type }}
      secrets:
        NPM_TOKEN: ${{ secrets.NPM_CYRILSTRONE_TOKEN }}
        ACCESS_GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN  }}
  ```
---

### Library Storybook ([library_storybook.yml](../.github/workflows/library_storybook.yml))

- Сборка и деплой Storybook для библиотеки.  
- Выполняет установку зависимостей, сборку Storybook и деплой собранной документации в отдельную ветку (`build_storybook`).  
- Использует приватные пакеты из GitHub Packages.
- Выбор `npm`, `yarn`, `bun`, по умолчанию `npm`.
- Пример использования:
  ```yml
  name: Library Storybook Wrapper

  on: workflow_dispatch

  jobs:
    call-publisher:
      uses: jenesei-software/.github/.github/workflows/library_storybook.yml@main
      secrets:
        ACCESS_GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN  }}
  ```