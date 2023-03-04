# Ren'Py Workflows

This repository is used to host and maintain several reusable workflows for Ren'Py projects. Add them to your projects by following the associated instructions.

## Generic instructions

When adding workflows to your projects, create files in your project in `.github/workflows/`.
You might also need to configure them in your project's `Settings`. Some configuration values are optional while others are required, look-up this information in the tables provided for each workflow.
To update your project's configuration, access its `Settings`, then open `Actions` in the `Secrets and variables` tab under `Security` to create a secret/variable for your workflows.

## Available workflows

### Lint your project

Linting the project helps ensure that there are no issues with the project's code.

| Name | Type | Requirement | Description |
| :-- | :-- | :--: | :-- |
| RENPY_VERSION | Variable | Optional | The Ren'Py version used by your project
| RENPY_DLC | Variable | Optional | The Ren'Py version used by your project

```yml
# .github/workflows/lint.yml
name: Lint project

on:
  push:
    branches:
      - master
      - main
  pull_request:
  workflow_dispatch:

jobs:
  call_linter:
    name: Basic validation
    uses: ayowel/renpy-workflows/.github/workflows/lint.yml@master
    with:
      renpy_version: ${{ vars.RENPY_VERSION }}
      renpy_dlcs: ${{ vars.RENPY_DLC }}
```

### Ensure the translation is up to date

Be warned when translation files were not updated in a pull request that requires it.

| Name | Type | Requirement | Description |
| :-- | :-- | :--: | :-- |
| TRANSLATION_LANGUAGES | Variable | Required | The languages supported by your game
| RENPY_VERSION | Variable | Optional | The Ren'Py version used by your project
| RENPY_DLC | Variable | Optional | The Ren'Py version used by your project
| TRANSLATION_KO_LABEL | Variable | Optional | A label to add to pull requests where translations are not up to date
| TRANSLATION_OK_LABEL | Variable | Optional | A label to add to pull requests where translations are up to date

```yml
# .github/workflows/translations.yml
name: Check translations

on:
  pull_request:

jobs:
  call_translation_check:
    name: Basic translation check
    uses: ayowel/renpy-workflows/.github/workflows/translation_check.yml@master
    permissions:
      pull-requests: write
      contents: read
    with:
      renpy_version: ${{ vars.RENPY_VERSION }}
      renpy_dlcs: ${{ vars.RENPY_DLC }}
      language: ${{ vars.TRANSLATION_LANGUAGES || 'None' }}
      error_label: ${{ vars.TRANSLATION_KO_LABEL }}
      success_label: ${{ vars.TRANSLATION_OK_LABEL }}
```

### Release the game to Itch.io

Manually start a release in a few clicks by running a release workflow from your GitHub interface.

| Name | Type | Requirement | Description |
| :-- | :-- | :--: | :-- |
| BUTLER_API_KEY | Secret | Required | The [upload key](https://itch.io/user/settings/api-keys) to use
| ITCH_USER | Variable | Required | The name of the user that distributes the game (ex: `renpytom`)
| ITCH_GAME | Variable | Required | The name of the game on Itch (ex: `the-question`)
| RENPY_VERSION | Variable | Optional | The Ren'Py version used by your project
| RENPY_DLC | Variable | Optional | The Ren'Py version used by your project

```yml
# .github/workflows/release.yml
name: Release Game

on:
  workflow_dispatch:
    inputs:
      version_number:
        description: "Version to release"
        required: true
        type: string
      itch_release:
        description: "Release to Itch"
        default: true
        type: boolean

jobs:
  call_release:
    name: Basic translation check
    uses: ayowel/renpy-workflows/.github/workflows/release.yml@master
    with:
      renpy_version: ${{ vars.RENPY_VERSION }}
      renpy_dlcs: ${{ vars.RENPY_DLC }}
      game_version: ${{ inputs.version_number }}
      release_itch: ${{ inputs.itch_release }}
    secrets:
      itch_user: ${{ vars.ITCH_USER }}
      itch_game: ${{ vars.ITCH_GAME }}
      itch_butler_key: ${{ secrets.BUTLER_API_KEY }}
```
