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
        id: build
        uses: jcdcdev/jcdcdev.Umbraco.Github.Build@main
        with:
          project-name: Umbraco.Community.SimpleDashboards
          project-path: src/Umbraco.Community.SimpleDashboards/Umbraco.Community.SimpleDashboards.csproj
          npm-working-dir: src/Umbraco.Community.SimpleDashboards.Client
          npm-enabled: true
      - name: Download package
        uses: actions/download-artifact@v4
        with:
          name: ${{ steps.build.outputs.artifact-name }}
          path: "./artifacts"
      - name: Push to NuGet
        shell: bash
        run: |
          dotnet nuget push ./artifacts/**/*.nupkg -k ${{ secrets.NUGET_API_KEY }} -s ${{ secrets.NUGET_SOURCE }} --skip-duplicate
```

#### **Inputs**

| Input                    | Description                                                                                                      | Required | Default          | Example                                        |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------- | -------- | ---------------- | ---------------------------------------------- |
| `project-name`           | Project name used when generating the uploaded artifact name.                                                    | Yes      | None             | `MyUmbracoProject`                             |
| `project-path`           | Path to the `.csproj` file to build and optionally pack.                                                         | Yes      | None             | `src/MyUmbracoProject/MyUmbracoProject.csproj` |
| `npm-working-dir`        | Working directory used for `npm install` and `npm run` when NPM support is enabled.                              | No       | None             | `src/MyUmbracoProject.Client`                  |
| `npm-enabled`            | Enables the Node.js setup and NPM build steps.                                                                   | No       | `false`          | `true`                                         |
| `npm-run-command`        | NPM script name passed to `npm run`.                                                                             | No       | `build`          | `build`                                        |
| `node-version`           | Node.js version used by `actions/setup-node`.                                                                    | No       | `22.x`           | `22.x`                                         |
| `dotnet-version`         | .NET SDK version used by `actions/setup-dotnet`.                                                                 | No       | `10.0.x`         | `10.0.x`                                       |
| `build-output`           | Build output path exposed to the action environment.                                                             | No       | `./build-output` | `./build-output`                               |
| `artifact-output`        | Output directory used for `dotnet build`, `dotnet pack`, and artifact upload.                                    | No       | `./build-assets` | `./build-assets`                               |
| `dotnet-pack`            | Runs the `dotnet pack` step after building the project.                                                          | No       | `true`           | `true`                                         |
| `strict-umbraco-version` | Fails the build when the computed GitVersion major does not match this action's supported Umbraco major version. | No       | `true`           | `false`                                        |

#### **Outputs**

| Output          | Description                         | Example                  |
| --------------- | ----------------------------------- | ------------------------ |
| `artifact-name` | The name of the generated artifact. | `MyUmbracoProject-1.0.0` |
| `version`       | The version of the package.         | `1.0.0`                  |

