# Automate Hasura DDN Deployments

This repository provides everything you need to automate the deployment of a Hasura v3 project on[Hasura DDN](https://hasura.io/ddn). Using GitHub Actions, it simplifies the process of running DDN CLI commands, allowing you to efficiently deploy connectors and build and deploy your supergraph with ease.

## Prerequisites & Process

1. A Hasura account
2. A personal access or service account access token.
3. Add the access token as a secret in your repository.
4. Configure the GitHub Action with the steps you require for your CI/CD pipeline.

## Prerequisites

### Hasura Account

A Hasura account is required to use this tool. You can sign up for a free account at [Hasura Cloud](https://console.hasura.io).

### Hasura Personal Access Token (PAT) or Service Account Access Token

A Hasura Personal Access Token (PAT) is required to authenticate with Hasura Cloud.

#### Option 1: Creating a PAT in Hasura Cloud

You can create a PAT from the [`Access Tokens` page of Hasura Cloud](https://cloud.hasura.io/account-settings/access-tokens).

#### Option 2: Logging in with the DDN CLI and printing your PAT

You can also log in with the DDN CLI with:

```bash
ddn auth login
```

Once that's completed you can print your PAT with:

```bash
ddn auth print-pat
```

#### Option 3: Using a Service Account Access Token

You can also use a service account access token to authenticate with Hasura DDN. See here for more information: [Service Accounts](https://hasura.io/docs/3.0/collaboration/service-accounts/)

### Adding the PAT to your repository

You'll then need to add the following secret to your repository:

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
        uses: hasura/ddn-deployment@2.3.0
        with:
          hasura-pat: ${{ secrets.HASURA_PAT }}
```

## Examples

### Automatic Builds on Every Commit on a branch and PRs to main branch

Imagine you have a branch called `main` that you use to create a DDN build. You can use the following workflow:

```yaml
on:
  push:
    branches:
      - main
      - release/*
  pull_request:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      - name: Install and Login to DDN CLI
        uses: hasura/ddn-deployment@2.3.0
        with:
          hasura-pat: ${{ secrets.HASURA_PAT }}

      - name: Deploy PG connector and update the connector link
        run: ddn connector build create --connector app/connector/mypg/connector.cloud.yaml --target-supergraph supergraph.cloud.yaml --target-connector-link mypg --project ${{ secrets.HASURA_PROJECT }}

      - name: Deploy mongodb connector and update the connector link
        run: ddn connector build create --connector app/connector/my_mongo/connector.cloud.yaml --target-supergraph supergraph.cloud.yaml --target-connector-link my_mongo --project ${{ secrets.HASURA_PROJECT }}

      - name: Build and deploy TS functions and update the connector link
        run: ddn connector build create --connector app/connector/myts/connector.cloud.yaml --target-supergraph supergraph.cloud.yaml --target-connector-link myts --project ${{ secrets.HASURA_PROJECT }}

      - name: Build supergraph
        run: ddn supergraph build create --supergraph ./supergraph.cloud.yaml --project ${{ secrets.HASURA_PROJECT }} --description "Build for commit ${{ github.sha }}"
```

### Automatic deployments + comment with build details on the PR

![alt text](image.png)

```yaml
permissions:
  contents: read
  pull-requests: write

on:
  pull_request:
    branches:
      - main

jobs:
  deploy:
    permissions: write-all
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Install and Login to DDN CLI
        uses: hasura/ddn-deployment@2.3.0
        with:
          hasura-pat: ${{ secrets.HASURA_PAT }}

      - name: Deploy PG connector and update the connector link
        run: ddn connector build create --connector app/connector/mypg/connector.cloud.yaml --target-supergraph supergraph.cloud.yaml --target-connector-link mypg --project ${{ secrets.HASURA_PROJECT }}

      - name: Deploy mongodb connector and update the connector link
        run: ddn connector build create --connector app/connector/my_mongo/connector.cloud.yaml --target-supergraph supergraph.cloud.yaml --target-connector-link my_mongo --project ${{ secrets.HASURA_PROJECT }}

      - name: Build and deploy TS functions and update the connector link
        run: ddn connector build create --connector app/connector/myts/connector.cloud.yaml --target-supergraph supergraph.cloud.yaml --target-connector-link myts --project ${{ secrets.HASURA_PROJECT }}

      - name: Install dependencies
        run: |
          sudo apt-get update
          sudo apt-get install -y jq

      - name: Build supergraph
        run: ddn supergraph build create --supergraph ./supergraph.cloud.yaml --project ${{ secrets.HASURA_PROJECT }} --description "Build for commit ${{ github.sha }}" --out=json > build_output.json

      - name: Extract URLs from JSON
        id: extract_urls
        run: |
          BUILD_URL=$(jq -r '.build_url' build_output.json)
          CONSOLE_URL=$(jq -r '.console_url' build_output.json)
          echo "build_url=$BUILD_URL" >> $GITHUB_ENV
          echo "console_url=$CONSOLE_URL" >> $GITHUB_ENV

      - name: Add PR comment with build details
        if: github.event_name == 'pull_request'
        uses: actions/github-script@v7
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          script: |
            const buildUrl = process.env.build_url;
            const consoleUrl = process.env.console_url;
            const prNumber = context.payload.pull_request.number;
            const commitId = context.sha;
            github.rest.issues.createComment({
              owner: context.repo.owner,
              repo: context.repo.repo,
              issue_number: context.issue.number,
              body: `Supergraph build was successful! 🎉\n\n**Build URL:** [${buildUrl}](${buildUrl})\n**Console URL:** [${consoleUrl}](${consoleUrl})\n**Commit ID:** ${commitId}`
            });
```

## Resources

Check out the [deployment guide](https://hasura.io/docs/3.0/ci-cd/overview) in our docs 🚀
