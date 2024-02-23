# Automate Hasura DDN Deployments

This repository contains the code to automate the deployment of a Hasura v3 project on
[Hasura DDN](https://hasura.io/ddn).

## Prerequisites

### Hasura Account

A Hasura account is required to use this tool. You can sign up for a free account at
[Hasura Cloud](https://console.hasura.io).

### Hasura Personal Access Token (PAT)

A Hasura Personal Access Token (PAT) is required to authenticate with Hasura Cloud. You can create a PAT from the
[`Access Tokens` page of Hasura Cloud](https://cloud.hasura.io/account-settings/access-tokens). **You'll then need to
add the following secret to your repository:**

```bash
HASURA_PAT: <your-hasura-pat>
```

### `staging` and `prod` environments on DDN

If you want to follow along with the examples below, you'll need to first create `staging` and `prod` environments on
Hasura DDN. You can do this using the [Hasura CLI](https://hasura.io/docs/3.0/cli/commands/environment-create).

## Usage

In any workflow, add the following step to deploy your Hasura project to Hasura DDN:

```yaml
- name: Deploy Hasura to <ENVIRONMENT>
  uses: hasura/hasura-ddn-deployment@v0.0.1
  with:
    hasura-pat: ${{ secrets.HASURA_PAT }}
    build-profile: <The build_profile — including the extension — you wish to use for this deployment>
    build-description: <A description to alert other users that this build was created using CI/CD>
```

## Examples

### Deploying to a staging environment

Imagine you have a branch called `release-stage` that you use to deploy to an environment on DDN called `staging`. You
can use the following workflow to deploy your Hasura project to the `staging` environment:

```yaml
name: Deploy Hasura to Staging

on:
  push:
    branches:
      - release-stage

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Deploy Hasura Project
        uses: hasura/ddn-deployment@0.0.2
        with:
          hasura-pat: ${{ secrets.HASURA_PAT }}
          build_profile: build-profile-staging.yaml
          build_description: "This build was created using CI/CD"
```

**Note:** The `build-profile-staging.yaml` file should be present in the root of your repository and should also be
referenced in the `hasura.yaml` file.

### Deploying to a production environment

Similar to the example above, imagine you have a branch called `release-prod` that you use to deploy to an environment
on DDN called `production`. You can use the following workflow to deploy your Hasura project to the `production`
environment:

```yaml
name: Deploy Hasura to Production

on:
  push:
    branches:
      - release-prod

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Deploy Hasura Project
        uses: hasura/ddn-deployment@0.0.2
        with:
          hasura-pat: ${{ secrets.HASURA_PAT }}
          build_profile: build-profile-prod.yaml
          build_description: "This build was created using CI/CD"
```

**Note:** The `build-profile-prod.yaml` file should be present in the root of your repository and should also be
referenced in the `hasura.yaml` file.

## Resources

Check out the [deployment guide](https://hasura.io/docs/3.0/ci-cd/overview) in our docs 🚀
