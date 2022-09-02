# ConfigCat Scan Repository Orb
This [CircleCI Orb](https://circleci.com/developer/orbs/orb/configcat/scan-repository) is a utility that discovers ConfigCat feature flag & setting usages in your source code and uploads the found code references to [ConfigCat](https://configcat.com).

For more information about repository scanning, see our [documentation](https://configcat.com/docs/advanced/code-references/overview).

## Setup
1. Create a new [ConfigCat Management API credential](https://app.configcat.com/my-account/public-api-credentials) and save its values in [CircleCI Environment Variables](https://circleci.com/docs/2.0/env-vars/#setting-an-environment-variable-in-a-project) with the following names: `CONFIGCAT_API_USER`, `CONFIGCAT_API_PASS`.

    ![secrets](https://raw.githubusercontent.com/configcat/scan-repository-orb/main/assets/secrets.png  "secrets")

2. [Get the ID of your ConfigCat Config](https://configcat.com/docs/advanced/code-references/overview#config-id) that you want to associate with your repository. The scanner will use this ID to determine which feature flags & settings to search for in your source code.

3. Create a new CircleCI YAML config in your GitHub repository under the `.circleci` folder, and put the following snippet into it. Don't forget to replace the `PASTE-YOUR-CONFIG-ID-HERE` value with your actual Config ID.
    ```yaml
    version: 2.1

    orbs:
      configcat: configcat/scan-repository@1.5.0

    workflows:
      main:
        jobs:
          - configcat/scan:
              config-id: PASTE-YOUR-CONFIG-ID-HERE # required
              file-url-template: 'https://github.com/your/repo/blob/{commitHash}/{filePath}#L{lineNumber}' # optional
              commit-url-template: 'https://github.com/your/repo/commit/{commitHash}' # optional
              # line-count: 3           # optional
              # sub-folder: 'src'       # optional
              # exclude-keys: >         # optional
              #   flag_key_to_exclue_1
              #   flag_key_to_exclue_2 
              # verbose: true           # optional
    ```

4. Commit & push your changes.

The above example configures a workflow that executes the scan and code references upload on every git `push` event.
Scan reports are uploaded for each branch of your repository that triggers the workflow. 

## Available Options

| Parameter             | Description                                                                | Required   | Default             |
| --------------------- | -------------------------------------------------------------------------- | ---------- | ------------------- |
| `api-host`            | ConfigCat Management API host.                                             | &#9745;    | `api.configcat.com` |
| `api-user`            | Name of the environment variable where the [ConfigCat Management API basic authentication username](https://app.configcat.com/my-account/public-api-credentials) is stored.                                                                                              | &#9745;    | CONFIGCAT_API_USER  |
| `api-pass`            | Name of the environment variable where the [ConfigCat Management API basic authentication password](https://app.configcat.com/my-account/public-api-credentials) is stored.                                                                                              | &#9745;    | CONFIGCAT_API_PASS  |
| `config-id`           | ID of the ConfigCat config to scan against.                                | &#9745;    |                     |
| `file-url-template`   | Template url used to generate VCS file links. Available template parameters: `commitHash`, `filePath`, `lineNumber`. Example: https://github.com/my/repo/blob/{commitHash}/{filePath}#L{lineNumber}                                                                    |            |                     |
| `commit-url-template` | Template url used to generate VCS commit links. Available template parameters: `commitHash`. Example: https://github.com/my/repo/commit/{commitHash}                                                                                         |            |                     |
| `line-count`          | Context line count before and after the reference line. (min: 1, max: 10)  |            | 4                   |
| `sub-folder`          | Sub-folder to scan, relative to the repository root folder.                |            |                     |
| `exclude-keys`        | List of feature flag keys that must be excluded from the scan report.      |            |                     |
| `verbose`             | Turns on detailed logging.                                                 |            | false               |
