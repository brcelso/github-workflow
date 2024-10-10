# GitHub Actions Workflow: Create Pull Request

This repository contains a GitHub Actions workflow created by **brcelso** designed to automate the process of creating a pull request (PR) from the `develop` branch to the `main` branch whenever there are new commits in `develop`. 

## Workflow Overview

The workflow is triggered by a push event to the `develop` branch. It performs the following steps:

1. **Checkout Repository**: Checks out the code to the runner environment.
2. **Check for New Commits**: Verifies if there are any new commits in the `develop` branch compared to the `main` branch.
3. **Create Pull Request**: If new commits are found, it creates a pull request with predefined details.

