# jcdcdev.Umbraco.GitHub.Build

This GitHub Action automates the process of building and packaging Umbraco projects. It supports various configurations and integrates with both .NET and NPM workflows. It also uses [GitVersion](https://gitversion.net/docs/) to automatically generate version numbers based on the project's Git history.

Looking to also create a GitHub release and push to NuGet? Check out [🚀 jcdcdev.Umbraco.GitHub.Release](https://github.com/jcdcdev/jcdcdev.Umbraco.GitHub.Release).

#### **Usage Example**
```yaml
name: 🏗️ Build & Push
on:
  pull_request:
  workflow_dispatch:
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Build
        uses: jcdcdev/jcdcdev.Umbraco.Github.Build@main
        with:
          project-name: jcdcdev.Umbraco.ExtendedDropdownEditor
          project-path: src/jcdcdev.Umbraco.ExtendedDropdownEditor/jcdcdev.Umbraco.ExtendedDropdownEditor.csproj
          npm-working-dir: src/jcdcdev.Umbraco.ExtendedDropdownEditor.Client
          npm-enabled: true
          umbraco-version: 15
          dotnet-version: "9"
      - name: Download package
        uses: actions/download-artifact@v4
        with:
          name: ${{ steps.build.outputs.artifact-name }}]
          path: "./artifacts"
    - name: Push to NuGet
      shell: bash
      run: |
        dotnet nuget push ./artifacts/**/*.nupkg -k ${{ secrets.NUGET_API_KEY }} -s ${{ secrets.NUGET_SOURCE }} --skip-duplicate
```

#### **Inputs**

| Input             | Description                                                     | Required | Example                  |
| ----------------- | --------------------------------------------------------------- | -------- | ------------------------ |
| `project-name`    | The name of the project.                                        | Yes      | `MyUmbracoProject`       |
| `project-path`    | The path to the project directory.                              | Yes      | `./src/MyUmbracoProject` |
| `umbraco-version` | The major version of Umbraco the project targets.               | Yes      | `10`                     |
| `npm-working-dir` | The working directory for NPM commands.                         | No       | `./src/MyUmbracoProject` |
| `npm-enabled`     | Enable NPM commands. Default is `false`.                        | No       | `true`                   |
| `npm-run-command` | The NPM command to run. Default is `build`.                     | No       | `build`                  |
| `npm-version`     | The version of node to use. Default is `20.x`.                  | No       | `20.x`                   |
| `dotnet-version`  | The version of .NET to use. Default is `8.0.x`.                 | No       | `8.0.x`                  |
| `build-output`    | The directory for build output. Default is `./build-output`.    | No       | `./build-output`         |
| `artifact-output` | The directory for artifact output. Default is `./build-assets`. | No       | `./build-assets`         |
| `dotnet-pack`     | Run `dotnet pack` command. Default is `true`.                   | No       | `true`                   |

#### **Outputs**

| Output          | Description                         | Example                  |
| --------------- | ----------------------------------- | ------------------------ |
| `artifact-name` | The name of the generated artifact. | `MyUmbracoProject-1.0.0` |
| `version`       | The version of the package.         | `1.0.0`                  |

