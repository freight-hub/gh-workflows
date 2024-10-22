# Github workflows

This repo contains reusable workflows for github actions.

More information on reusable workflows can be found [here](https://docs.github.com/en/actions/using-workflows/reusing-workflows)

## Public

Please keep in mind that this is a public repository.

# Node workflows

## node-build-lint-test.yml

Basic worfkflow for nodejs.


# Package workflows

## package-build-check-publish.yml

Will publish the package based on the new SemVer found within the repository (tags).

If this is a required build step, you should pass in a PAT because the default `secrets.GITHUB_TOKEN` will NOT trigger the a workflow (to prevent recurisive flows).
We explicitly want this because we update the `package.json` with the newer version and need the required checks to pass.

### Usage:

```yaml
name: "[PR] Build, check and publish"

on:
  pull_request:
    types: [labeled, opened, reopened, synchronize, edited]
    branches:
      - main
    paths:
      - '.github/workflows/**'
      - 'src/**'
      - 'yarn.lock'

jobs:
  pre-release:
    permissions:
      id-token: write
    uses: freight-hub/gh-workflows/.github/workflows/package-build-check-publish.yml@main
```


## package-build-check-publish-ignore.yml

A empty file used to pass the required checks when `package.json` has been updated from the previous command.


### Usage:

```yaml
# This workflow will trigger, but only add the status check after the CI check has run
name: "[PR] Build, check and publish"

on:
  pull_request:
    branches:
      - main
    paths:
      - 'package.json'

jobs:
  pre-release:
    permissions:
      id-token: write
    uses: freight-hub/gh-workflows/.github/workflows/package-build-check-publish-ignore.yml@main
```

## package-publish.yml

Will install, build and publish the package with the version from package.json. Will create a new release with tag.

### Usage:

```yaml
name: "[Main] Build, check and publish"

on:
  pull_request:
    types:
      - closed
    branches:
      - main

jobs:
  publish-main:
    permissions:
      id-token: write
    uses: freight-hub/gh-workflows/.github/workflows/package-publish-main.yml@main
    secrets: inherit
```

## node-localstack-build-lint-test.yml

Will add the localstack docker image to the testing environment.
You can provide a Makefile with an install script called `aws-setup` to be executed
before the tests will run. AWS-CLI will be installed.

### Usage:

```yaml
name: "Build test with localStack"

on:
  pull_request:
    types:
      - closed
    branches:
      - main

jobs:
  publish-main:
    permissions:
      id-token: write
    uses: freight-hub/gh-workflows/.github/workflows/node-localstack-build-lint-test.yml@main
    secrets: inherit
```