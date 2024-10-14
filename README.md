# GitHub Actions Workflow: Pull Request Automation + Discord Notifications + Docker Integration

This repository contains a GitHub Actions workflow, created by brcelso, to automate pull request creation between the develop and main branches, and sends notifications via Discord Webhooks. Additionally, the workflow is Docker-integrated, ensuring that the environment setup and dependencies are containerized for consistency and ease of use.
Key Components

    GitHub Actions: Automates the workflow of creating pull requests and running Docker containers.
    Discord Webhooks: Notifies a Discord channel about the status of the workflow, such as successful pull request creation or any errors.
    Docker: Ensures a consistent environment for executing steps in the workflow.

Workflow Overview

The workflow is triggered by a push event to the develop branch and follows these steps:

    Checkout Repository:
        The workflow checks out the code from the develop branch into the GitHub Actions runner environment using the actions/checkout@v2 action.

    Build Docker Environment:
        Using a Dockerfile in the repository, the workflow builds a Docker container to ensure a consistent environment for running the subsequent steps.
        This container allows you to define specific dependencies and runtime settings for the code.

    Check for New Commits:
        The workflow compares the develop branch with the main branch to check if there are any new commits in develop that haven't yet been merged into main.

    Create Pull Request:
        If new commits are found, it creates a pull request automatically using the peter-evans/create-pull-request@v3 action, with pre-defined details (such as title, description, and branch info).

    Notify via Discord Webhook:
        After successfully creating a pull request or encountering an error, a notification is sent to a specified Discord channel using a Discord Webhook.
        The message will include relevant details such as:
            Pull request link
            Commits included
            Status of the workflow (success or failure)

Setting Up the Workflow

To set up this GitHub Actions workflow, follow these steps:

    Configure the Discord Webhook:
        In your Discord server, create a webhook in the desired channel.
        Copy the webhook URL and store it as a secret in your GitHub repository (DISCORD_WEBHOOK_URL).

    Dockerfile:
        Ensure you have a Dockerfile in the root of your repository for building the container that the workflow will run in.
        Example Dockerfile:

        Dockerfile

    FROM node:14
    WORKDIR /app
    COPY package*.json ./
    RUN npm install
    COPY . .
    CMD ["npm", "run", "start"]

GitHub Actions Workflow (YAML):

    Here's an example of the workflow (.github/workflows/pr-workflow.yml):

    yaml

        name: Pull Request Automation with Discord Notifications

        on:
          push:
            branches:
              - develop

        jobs:
          create-pr:
            runs-on: ubuntu-latest
            steps:
              - name: Checkout repository
                uses: actions/checkout@v2

              - name: Build Docker container
                run: docker build -t my-app .

              - name: Check for new commits
                run: |
                  git fetch origin main
                  git log --oneline origin/main..origin/develop

              - name: Create Pull Request
                uses: peter-evans/create-pull-request@v3
                with:
                  base: main
                  head: develop
                  title: "Auto-generated PR: Merge develop into main"
                  body: "This PR was automatically created by the GitHub Actions workflow."

              - name: Send notification to Discord
                run: |
                  curl -H "Content-Type: application/json" \
                  -d '{"content": "Pull request created successfully: https://github.com/your_repo/pull_request_link"}' \
                  ${{ secrets.DISCORD_WEBHOOK_URL }}

Secrets

In your GitHub repository, you'll need to configure the following secret:

    DISCORD_WEBHOOK_URL: The URL of your Discord Webhook to send notifications.

Benefits of Using Docker

By utilizing Docker, we ensure that the environment in which the workflow runs is consistent and reproducible, regardless of where it is executed. Docker isolates dependencies and configurations, preventing "works on my machine" issues when running code in different environments.
Docker Advantages:

    Consistency: Ensure that the workflow always runs in a predictable environment.
    Portability: Run the same container locally or in any CI/CD pipeline.
    Isolation: Encapsulate dependencies and configurations inside the container.

Conclusion

This workflow integrates GitHub Actions, Discord Webhooks, and Docker to automate pull request creation, ensure consistent environments, and keep your team informed via Discord. This setup can be easily customized to fit other automation or notification needs for your repository.GitHub Actions Workflow: Pull Request Automation + Discord Notifications + Docker Integration

This repository contains a GitHub Actions workflow, created by brcelso, to automate pull request creation between the develop and main branches, and sends notifications via Discord Webhooks. Additionally, the workflow is Docker-integrated, ensuring that the environment setup and dependencies are containerized for consistency and ease of use.
Key Components

    GitHub Actions: Automates the workflow of creating pull requests and running Docker containers.
    Discord Webhooks: Notifies a Discord channel about the status of the workflow, such as successful pull request creation or any errors.
    Docker: Ensures a consistent environment for executing steps in the workflow.

Workflow Overview

The workflow is triggered by a push event to the develop branch and follows these steps:

    Checkout Repository:
        The workflow checks out the code from the develop branch into the GitHub Actions runner environment using the actions/checkout@v2 action.

    Build Docker Environment:
        Using a Dockerfile in the repository, the workflow builds a Docker container to ensure a consistent environment for running the subsequent steps.
        This container allows you to define specific dependencies and runtime settings for the code.

    Check for New Commits:
        The workflow compares the develop branch with the main branch to check if there are any new commits in develop that haven't yet been merged into main.

    Create Pull Request:
        If new commits are found, it creates a pull request automatically using the peter-evans/create-pull-request@v3 action, with pre-defined details (such as title, description, and branch info).

    Notify via Discord Webhook:
        After successfully creating a pull request or encountering an error, a notification is sent to a specified Discord channel using a Discord Webhook.
        The message will include relevant details such as:
            Pull request link
            Commits included
            Status of the workflow (success or failure)

Setting Up the Workflow

To set up this GitHub Actions workflow, follow these steps:

    Configure the Discord Webhook:
        In your Discord server, create a webhook in the desired channel.
        Copy the webhook URL and store it as a secret in your GitHub repository (DISCORD_WEBHOOK_URL).

    Dockerfile:
        Ensure you have a Dockerfile in the root of your repository for building the container that the workflow will run in.
        Example Dockerfile:

        Dockerfile

    FROM node:14
    WORKDIR /app
    COPY package*.json ./
    RUN npm install
    COPY . .
    CMD ["npm", "run", "start"]

GitHub Actions Workflow (YAML):

    Here's an example of the workflow (.github/workflows/pr-workflow.yml):

    yaml

        name: Pull Request Automation with Discord Notifications

        on:
          push:
            branches:
              - develop

        jobs:
          create-pr:
            runs-on: ubuntu-latest
            steps:
              - name: Checkout repository
                uses: actions/checkout@v2

              - name: Build Docker container
                run: docker build -t my-app .

              - name: Check for new commits
                run: |
                  git fetch origin main
                  git log --oneline origin/main..origin/develop

              - name: Create Pull Request
                uses: peter-evans/create-pull-request@v3
                with:
                  base: main
                  head: develop
                  title: "Auto-generated PR: Merge develop into main"
                  body: "This PR was automatically created by the GitHub Actions workflow."

              - name: Send notification to Discord
                run: |
                  curl -H "Content-Type: application/json" \
                  -d '{"content": "Pull request created successfully: https://github.com/your_repo/pull_request_link"}' \
                  ${{ secrets.DISCORD_WEBHOOK_URL }}

Secrets

In your GitHub repository, you'll need to configure the following secret:

    DISCORD_WEBHOOK_URL: The URL of your Discord Webhook to send notifications.

Benefits of Using Docker

By utilizing Docker, we ensure that the environment in which the workflow runs is consistent and reproducible, regardless of where it is executed. Docker isolates dependencies and configurations, preventing "works on my machine" issues when running code in different environments.
Docker Advantages:

    Consistency: Ensure that the workflow always runs in a predictable environment.
    Portability: Run the same container locally or in any CI/CD pipeline.
    Isolation: Encapsulate dependencies and configurations inside the container.

Conclusion

This workflow integrates GitHub Actions, Discord Webhooks, and Docker to automate pull request creation, ensure consistent environments, and keep your team informed via Discord. This setup can be easily customized to fit other automation or notification needs for your repository.
