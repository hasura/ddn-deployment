# Automate Hasura DDN Deployments

This repository contains the code to automate the deployment of a Hasura v3 project on [Hasura DDN](https://hasura.io/ddn).

## Prerequisites

### Hasura Account

A Hasura account is required to use this tool. You can sign up for a free account at [Hasura Cloud](https://console.hasura.io).

### Hasura Personal Access Token (PAT)

A Hasura Personal Access Token (PAT) is required to authenticate with Hasura Cloud. You can create a PAT from the [`Access Tokens` page of Hasura Cloud](https://cloud.hasura.io/account-settings/access-tokens). **You'll then need to add the following secret to your repository:**

```bash
HASURA_PAT: <your-hasura-pat>
```

## Usage

In any workflow, add the following steps to automate the deployment of your Hasura project to Hasura DDN:

```yaml
name: Hasura DDN Build

on:
  push:
    branches:
      - main
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Hasura DDN Build
        uses: hasura/ddn-deployment@0.0.2
        with:
          hasura-pat: ${{ secrets.HASURA_PAT }}
          build_description: "This build was created using CI/CD"
```

## Examples

### Automatic Builds on Every Commit on a branch

Imagine you have a branch called `main` that you use to create a DDN build. You can use the following workflow:

```yaml
name: Automatic Builds from main branch

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Deploy Hasura Project
        uses: hasura/ddn-deployment@0.0.2
        with:
          hasura-pat: ${{ secrets.HASURA_PAT }}
          build_description: "This build was created using CI/CD"
```

## Resources

Check out the [deployment guide](https://hasura.io/docs/3.0/ci-cd/overview) in our docs 🚀
