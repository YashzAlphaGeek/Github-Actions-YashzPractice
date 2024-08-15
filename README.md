# Github-Examples

A repository containing GitHub Actions examples for programmatic use.

## GitHub Actions Workflows

This repository includes the following GitHub Actions workflows located in the `.github/workflows` directory:

### 1. [`greetings.yml`](https://github.com/YashzAlphaGeek/Github-Examples-YashzPractice/blob/main/.github/workflows/greetings.yml)
- **Purpose**: This workflow sends a greeting message whenever a new issue or pull request is created. It’s a simple way to automate a welcoming response to contributors.
- **Trigger**: Activated automatically on the creation of issues or pull requests.

### 2. **[`manualeventtrigger.yml`](https://github.com/YashzAlphaGeek/Github-Examples-YashzPractice/blob/main/.github/workflows/manualeventtrigger.yml)**:
   - **Purpose**: This workflow allows you to manually trigger actions in the repository. It's useful for workflows that need to be run on demand rather than automatically.
   - **Trigger**: This workflow is triggered manually through the GitHub Actions interface.

### 3. **[`multi-event.yml`](https://github.com/YashzAlphaGeek/Github-Examples-YashzPractice/blob/main/.github/workflows/multi-event.yml)**:
   - **Purpose**: Handles multiple events, such as push and pull requests, to perform tasks like testing or deployment across different branches or event types.
   - **Trigger**: Runs on multiple events including pushes to the repository and pull request actions.
     
### 4. **[`schedule_two_min.yml`](https://github.com/YashzAlphaGeek/Github-Examples-YashzPractice/blob/main/.github/workflows/schedule_two_min.yml)**:
   - **Purpose**: This workflow is scheduled to run every two minutes, allowing for regular checks, updates, or automated tasks that need frequent execution.
   - **Trigger**: Triggered by a cron schedule every two minutes.
  
Each of these workflows is defined in its respective YAML file, which can be navigated directly via the provided links.

### Workflow File

```yaml
# This is a comprehensive GitHub Actions workflow example covering multiple use cases.

name: Comprehensive Workflow Example

# Define the event triggers for this workflow.
on:
  # Trigger the workflow on push events to the specified branches
  push:
    branches:
      - main
      - develop
  # Trigger the workflow on pull requests targeting the specified branches
  pull_request:
    branches:
      - main
  # Allow manual triggering of the workflow
  workflow_dispatch:
    inputs:
      example_input:
        description: 'An example input parameter for manual triggering'
        required: true
        default: 'default value'
  # Schedule the workflow to run at specific times
  schedule:
    - cron: '0 0 * * *'  # Runs daily at midnight

jobs:
  # Job to run tests on different Node.js versions using a matrix
  test:
    runs-on: ubuntu-latest

    # Define a matrix to run the job on different Node.js versions
    strategy:
      matrix:
        node-version: [12.x, 14.x, 16.x]

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node-version }}

      - name: Install dependencies
        run: npm install

      - name: Run tests
        run: npm test

  # Job to deploy the application
  deploy:
    runs-on: ubuntu-latest
    needs: test  # This job will only run after the 'test' job completes successfully

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Deploy to Production
        env:
          API_KEY: ${{ secrets.API_KEY }}  # Access secret API key from GitHub Secrets
        run: |
          echo "Deploying to production..."
          # Commands to deploy the application, e.g., using a deployment script

  # Job to notify when workflow fails
  notify:
    runs-on: ubuntu-latest
    if: failure()  # This job only runs if any of the previous jobs fail

    steps:
      - name: Notify on Failure
        run: |
          echo "The workflow failed. Please check the logs for more details."
          # Commands to send notifications, e.g., via email or messaging service

  # Job to build Docker image
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Build Docker Image
        run: |
          docker build -t my-app:latest .
          docker tag my-app:latest my-repo/my-app:latest
          docker push my-repo/my-app:latest
