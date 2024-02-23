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
hasura-pat: <your-hasura-pat>
```

## Usage

In any workflow, add the following step to deploy your Hasura project to Hasura DDN:

```yaml
- name: Deploy Hasura Project
  uses: hasura/hasura-ddn-deployment@v0.0.1
  with:
    hasura-pat: ${{ secrets.hasura-pat }}
    build-profile: <The build_profile — including the extension — you wish to use for this deployment>
    build-description: <A description to alert other users that this build was created using CI/CD>
```

## Examples

### Deploying to a staging environment

Imagine you have a branch called `release-stage` that you use to deploy to an environment on DDN called `staging`. You
can use the following workflow to deploy your Hasura project to the `staging` environment:

```yaml
name: Deploy to Staging

on:
  push:
    branches:
      - release-stage

jobs:
    deploy:
        runs-on: ubuntu-latest
        steps:
        - name: Deploy Hasura Project
            uses: hasura/hasura-ddn-deployment@v0.0.1
            with:
            hasura-pat: ${{ secrets.hasura-pat }}
            build-profile: build-profile-staging.yaml
            build-description: "This build was created using CI/CD"
```

**Note:** The `build-profile-staging.yaml` file should be present in the root of your repository and should also be
referenced in the `hasura.yaml` file.

### Deploying to a production environment

Similar to the example above, imagine you have a branch called `release-prod` that you use to deploy to an environment
on DDN called `production`. You can use the following workflow to deploy your Hasura project to the `production`
environment:

```yaml
name: Deploy to Production

on:
  push:
    branches:
      - release-prod

jobs:
    deploy:
        runs-on: ubuntu-latest
        steps:
        - name: Deploy Hasura Project
            uses: hasura/hasura-ddn-deployment@v0.0.1
            with:
            hasura-pat: ${{ secrets.hasura-pat }}
            build-profile: build-profile-prod.yaml
            build-description: "This build was created using CI/CD"
```

**Note:** The `build-profile-prod.yaml` file should be present in the root of your repository and should also be
referenced in the `hasura.yaml` file.

## Resources

Check out the [deployment guide](#) in our docs 🚀
