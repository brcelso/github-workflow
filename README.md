# Pull requests from develop, and a Docker image on main

Pushes to this repository run two GitHub Actions workflows. Together they keep `main` reviewable and publish a container image from the commit that lands there.

**Create PR** (`.github/workflows/create-pr.yml`) runs on every push to `develop`. It checks out the repository, fetches `main`, and lists commits that are on `origin/develop` and not on `origin/main`. It then looks for a pull request from `develop` into `main`. When none is found, it opens one with the GitHub CLI: title `PR created`, body `Automated Creation`, assignee `brcelso`. Work on `develop` gets a pull request to review before it merges, without opening that pull request by hand.

That commit listing finishes successfully either way, including when `develop` has nothing new relative to `main`. It does not decide whether a pull request is created. The following step does: the workflow creates a pull request only when it finds no existing pull request from `develop` into `main`.

**Release** (`.github/workflows/release.yml`) runs on every push to `main`. It checks out that commit, builds the root `Dockerfile` as `celsosjunior/github-workflow`, logs in to Docker Hub, and pushes the image. A push to `main` — including a merge of the pull request opened above — publishes the image that matches the repository at that commit.

## Setup

1. Keep branches named `develop` and `main`. Pushes to `develop` start Create PR. Pushes to `main` start Release.
2. Leave these files in the commit that is pushed. The workflows read them from that commit:
   - `.github/workflows/create-pr.yml`
   - `.github/workflows/release.yml`
   - `Dockerfile`
   - `package.json` and `package-lock.json` (copied into the image)
3. Enable GitHub Actions on the repository. Create PR and Release both use `ubuntu-latest`, which provides Docker and the GitHub CLI (`gh`).
4. Add the secrets in the next section as repository secrets: **Settings → Secrets and variables → Actions**. The workflow files do not set a GitHub Environment.
5. Store a GitHub personal access token in `secrets_PAT`. Create PR passes that token to the GitHub CLI as `GH_TOKEN` for `gh pr list` and `gh pr create`. The token needs access to this repository and permission to open pull requests assigned to `brcelso`. The job also requests `contents: write` and `pull-requests: write` for the default token. The CLI steps use `secrets_PAT`.
6. Use a Docker Hub account that can push `celsosjunior/github-workflow`. Release logs in with `DOCKERHUB_USERNAME` and `DOCKER_PASSWORD`, then pushes that name. The build command sets no tag, so Docker uses `latest`.

The image is the root `Dockerfile`: base image `node:14`, working directory `/app`, `npm install` from the package manifests, port `3000`, and command `npm start`. `package.json` provides a `test` script.

## Required secrets

| Secret | Workflow | Type |
| --- | --- | --- |
| `secrets_PAT` | Create PR | GitHub personal access token |
| `DOCKERHUB_USERNAME` | Release | Docker Hub username |
| `DOCKER_PASSWORD` | Release | Docker Hub password or access token |

These are names only. Store the values in GitHub Actions secrets.

## How to use

### Open the pull request

1. Commit on `develop` and push to `origin`.
2. On GitHub, open **Actions** and select the **Create PR** run for that push.
3. The job checks out the repository with `actions/checkout@v4`, fetches `origin/main`, and lists commits in `origin/main..origin/develop`.
4. It runs `gh pr list --base main --head develop --json url`. Output that contains a URL sets `pr_exists=true` and skips creation. Empty output sets `pr_exists=false`.
5. When `pr_exists` is `false`, it runs `gh pr create --base main --head develop --title "PR created" --body "Automated Creation" --assignee brcelso`.

Review that pull request and merge it when it is ready.

### Publish the image

1. Push to `main`, usually by merging the pull request.
2. Open the **Release** run for that push.
3. The job checks out the commit with `actions/checkout@v4`, runs `docker build . -t celsosjunior/github-workflow`, logs in to Docker Hub, and runs `docker push celsosjunior/github-workflow`.

Build the same image locally with:

```bash
docker build -t celsosjunior/github-workflow .
```

## License

MIT. Copyright (c) 2024 Celso da Silva Junior. See [LICENSE](LICENSE).
