## Описание глобальных Workflows

### Deploy Frontend ([deploy_frontend.yml](../.github/workflows/deploy_frontend.yml))

- Автоматическая сборка фронтенд-проекта.
- Выполняет клонирование репозитория, установку зависимостей, сборку проекта для текущей ветки и деплой в отдельную ветку (`build_<branch>`).
- Выбор `npm`, `yarn`, `bun`, `pnpm`, по умолчанию `npm`.
- Поддерживает автоматическое обновление версии (major, minor, patch) и работу с приватными пакетами GitHub Packages.

- Пример использования:

  ```yml
  name: Deploy Frontend Wrapper

  on:
    workflow_dispatch:
      inputs:
        version_type:
          description: "Version bump"
          required: false
          default: "none"
          type: choice
          options:
            - none
            - major
            - minor
            - patch
        package_manager:
          description: "Package manager"
          required: false
          default: "npm"
          type: choice
          options:
            - npm
            - yarn
            - bun
            - pnpm
        add_branch_name:
          description: 'Add branch name to version'
          required: false
          type: boolean
          default: true
        add_build_number:
          description: 'Add build number to version'
          required: false
          type: boolean
          default: true
  jobs:
    call-publisher:
      uses: jenesei-software/.github/.github/workflows/deploy_frontend.yml@main
      with:
        version_type: ${{ inputs.version_type }}
        package_manager: ${{ inputs.package_manager }}
        add_branch_name: ${{ inputs.add_branch_name }}
        add_build_number: ${{ inputs.add_build_number }}
      secrets:
        ACCESS_GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN  }}
  ```

---

### Deploy Library ([deploy_library.yml](../.github/workflows/deploy_library.yml))

- Публикация npm-библиотеки в выбранный реестр: GitHub Packages, npm или оба сразу.
- Выполняет автоматический bump версии, пушит теги и коммиты, проверяет наличие версии в реестрах и публикует пакет только если версия ещё не опубликована.
- Поддерживает работу с приватными пакетами и опциональный токен для публикации в npm.
- Выбор `npm`, `yarn`, `bun`, `pnpm`, по умолчанию `npm`.
- Пример использования:

  ```yml
  name: Deploy Library Wrapper

  on:
    workflow_dispatch:
      inputs:
        registry_type:
          description: "Registry type"
          required: true
          default: "npm"
          type: choice
          options:
            - npm
            - github
            - all
        version_type:
          description: "Version bump"
          required: true
          default: "patch"
          type: choice
          options:
            - major
            - minor
            - patch
        package_manager:
          description: "Package manager"
          required: false
          default: "npm"
          type: choice
          options:
            - npm
            - yarn
            - bun
            - pnpm

  jobs:
    call-publisher:
      uses: jenesei-software/.github/.github/workflows/deploy_library.yml@main
      with:
        registry_type: ${{ inputs.registry_type }}
        version_type: ${{ inputs.version_type }}
        package_manager: ${{ inputs.package_manager }}
      secrets:
        NPM_TOKEN: ${{ secrets.NPM_CYRILSTRONE_TOKEN }}
        ACCESS_GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN  }}
  ```
