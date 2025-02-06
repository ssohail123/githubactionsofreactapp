# Deploy React to GitHub Pages

This project demonstrates how to deploy a React application to GitHub Pages using GitHub Actions. The workflow includes testing, building, and deploying the application, along with a SonarCloud scan for code quality analysis.


## Workflow Overview

The workflow is defined in the GitHub Actions configuration file (`.github/workflows/deploy.yml`). It consists of three main jobs:

1. **Test**: Runs tests to ensure the code is working as expected.
2. **Build**: Builds the React application for production.
3. **Deploy**: Deploys the built application to GitHub Pages.

Additionally, there is a separate workflow for SonarCloud code analysis that runs on every push or pull request to the `master` branch.


## Why Caching is Used

Caching is used because each job runs in an **isolated environment**, so dependencies need to be reinstalled unless cached.


## Workflow Details

### 1. **Test Job**
- **Trigger**: Runs on every `push` or manual trigger (`workflow_dispatch`).
- **Steps**:
  - **Checkout Code**: Uses `actions/checkout@v4` to fetch the repository code.
  - **Set Up Node.js**: Installs Node.js version 20 using `actions/setup-node@v4`.
  - **Cache Dependencies**: Caches npm dependencies using `actions/cache@v4`.
  - **Install Dependencies**: Runs `npm ci` to install dependencies.
  - **Run Tests**: Executes tests using `npm run test`.

### 2. **Build Job**
- **Dependency**: Depends on the `test` job to complete successfully.
- **Steps**:
  - **Checkout Code**: Fetches the repository code.
  - **Set Up Node.js**: Installs Node.js version 20.
  - **Cache Dependencies**: Caches npm dependencies.
  - **Install Dependencies**: Runs `npm ci` to install dependencies.
  - **Build Project**: Builds the React application using `npm run build`.
  - **Upload Artifact**: Uploads the `dist` folder as an artifact using `actions/upload-pages-artifact@v3`.

### 3. **Deploy Job**
- **Dependency**: Depends on the `build` job to complete successfully.
- **Permissions**: Grants the `GITHUB_TOKEN` the necessary permissions to deploy to GitHub Pages.
- **Environment**: Deploys to the `github-pages` environment.
- **Steps**:
  - **Deploy to GitHub Pages**: Uses `actions/deploy-pages@v4` to deploy the artifact to GitHub Pages.


## SonarCloud Integration

A separate workflow (`sonarcloud.yml`) is configured to run a SonarCloud scan on every push to the `master` branch or when a pull request is opened, synchronized, or reopened.

### SonarCloud Job
- **Trigger**: Runs on `push` to `master` or `pull_request` events.
- **Steps**:
  - **Checkout Code**: Fetches the repository code with `fetch-depth: 0` for accurate analysis.
  - **SonarCloud Scan**: Uses `SonarSource/sonarcloud-github-action@master` to perform the scan.
  - **Environment Variables**:
    - `GITHUB_TOKEN`: Required to fetch pull request information.
    - `SONAR_TOKEN`: Required for authentication with SonarCloud.
