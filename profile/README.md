# Doppler Projects

## Minimal requirements for new projects

- Only choose private repositories when the code has business value differentiator logic.

- Use [FromDoppler Organization](https://github.com/FromDoppler/) for public repositories and [MakingSense](https://github.com/MakingSense) for private ones.

- Do not store secrets in the repository

  If you really need to store secrets, store them encrypted using SOPS ([see more information](https://github.com/MakingSense/doppler-swarm#secrets)).

- Add a `README.md` file with:

  - Basic description of the project scope

  - Minimal architecture design

  - Guide for contributors

  - Coding standard (When it is not covered by an automated CI process)

- CI/CD from the beginning

  - Setup CI/CD and all environments even before adding any real functionality

  - Branch protection rules to avoid CI bypass

  - Example automated test even before adding any real functionality

## Nice to have

- Consider base your repository in one of our base projects [See more](#base-projects)

- Validate coding standards using automated CI process [See more](#ci-cd)

- Follow Doppler convention for branches and environments [See more](#ci-cd)

- Generate a version.txt file, or another simple way to check what is the code related to the published system. See more details below. [See more](#version-txt)

- Authenticate users using JWT with our public key [See more]{#doppler-security}

### Base projects

We have some seed projects that can be used as the base for new projects:

- [Hello Microservice](https://github.com/FromDoppler/hello-microservice) - Simple .NET API project with CI and CD to Dockerhub

- [Hello WebApp](https://github.com/FromDoppler/hello-webapp/) - Simple React project (based in CRA) with CI and CD to our CDN

Sometimes, those seed projects can be outdated or we did not prepare a seed yet, so, you can base your project on [an existing one](https://github.com/orgs/FromDoppler/repositories?q=&type=all&language=&sort=), for example:

- [Doppler System Usage](https://github.com/FromDoppler/doppler-system-usage) - Simple Node based AWS Lambda Functions API with CI and CD to our AWS using Serverless

- [Doppler Session MFE](https://github.com/FromDoppler/doppler-session-mfe) - Minimal micro-frontend without UI (based in CRA) with CI and CD to our CDN

- [Doppler HTML Editor API](https://github.com/FromDoppler/doppler-html-editor-api) - Simple .NET API project with CI and CD to Dockerhub with an interesting data access layer architecture based on Dapper

- [Doppler Editors WebApp](https://github.com/FromDoppler/doppler-editors-webapp) - Simple React project (based in CRA) with CI and CD to our CDN with React Router, a dependency injection layer, React Query and others

### CI CD

We try to automate all our tests and validations in the CI/CD process.

A posible approach to do this is using our dedicated Jenkins service that runs docker based builds and configure _branch protection rules_ in GitHub. You can see an example in [Hello Microservice repository](https://github.com/FromDoppler/hello-microservice#hello-microservice).

In general, we have three environments binding to git in this way:

- Resetting the `INT` branch generates a build and deploys it to the INT environment
- Merging in `main` generates a build and deploys it to the QA environment
- Creating a tag with format `/v\d+.\d+.\d+/` generates a build and deploys it to the Production environment

See a detailed example at [Doppler Forms repository](https://github.com/MakingSense/doppler-forms/blob/master/README.md#continuous-deployment-to-test-and-production-environments)

### Version txt

Knowing exactly what source code corresponds to the system we are running use to be very useful.

In our APIs projects we expose a `version.txt` for that, for example:

```http
GET https://apis.fromdoppler.com/hello/version.txt

# Response
# dopplerdock/hello-microservice:v1.2.4_398120d4821b9da27fd49a485ac58048ee768be8
```

In our CDN based micro-frontend projects, we expose the version in the manifest file:

```http
GET https://cdn.fromdoppler.com/editors-webapp/asset-manifest-v1.json

# Response
# {
#   "canonicalVersion": "v1.2.5_209dace9d57b9a0f6eef5699fe42e22519083aee",
#   "files": {
#     // . . .
# }
```

The version format is the following:

```bnf
<valid_version_info> ::= <full_version>
                      | <artifact_repo> ":" <full_version>
                      | <full_version> "@" <source_code_repo_url>
                      | <artifact_repo> ":" <full_version> "@" <source_code_repo_url>

<full_version> ::= <name_or_version> "+" <source_code_commit_id>
                | <name_or_version> "_" <source_code_commit_id>

<name_or_version> ::= <semver_version>
                    | <name>

<name> ::= "INT"
        | "main"
        | "master"
        | "TEST"
        | "develop"

<semver version> ::= "v" <mayor> "." <minor> "." <patch>

<mayor> ::= <digits>

<minor> ::= <digits>

<patch> ::= <digits>

<artifact_repo> ::= <docker_hub_org> "/" <docker_hub_repo>
# TODO: add more alternatives for <artifact_repo>

# <digits> matches /\d+/
# <docker_hub_org> is any valid DockerHub organization name
# <docker_hub_repo> is any valid DockerHub repository name
# <source_code_repo_url> is any valid URL

# Full regex: /(?:(?<artifact_repo>(?<docker_hub_org>[\w-]+)\/(?<docker_hub_repo>[\w-]+)):)?(?<version_name>(?<version>v(?<mayor>\d+)\.(?<minor>\d+)\.(?<patch>\d+))|(?<name>INT|main|master|TEST|develop))[_\+](?<source_code_commit_id>\w+)(?:@(?<source_code_repo_url>.+))?/
```

### Doppler Security

When we need a quick and easy way of authenticate a request, we can validate the request JWT token against a public key.

We have a complete and flexible implementation of this token validation in .NET (for example in [Hello Microservice](https://github.com/FromDoppler/hello-microservice/pull/12)),
but it can be easily re-implemented with other frameworks and languages.

### FromDoppler GitHub Packages

To use our generated artifacts, follow the next steps

- Generate a [GitHub personal access token](https://github.com/settings/tokens/new) with at least `read:packages` permission
- Set and use the environment variable `FROMDOPPLER_GITHUB_PACKAGES_TOKEN` with the token generated

### Configure FromDoppler GitHub Packages as NuGet source

- Use or adapt the [`nuget.config`](https://github.com/FromDoppler/.github/blob/main/dotnet-examples/nuget.config) file example into the required repository
