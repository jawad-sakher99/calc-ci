# calc-ci

A small Python calculator with a Jenkins CI pipeline.

## Run Jenkins

1. Build the Jenkins image (it adds the Docker CLI):

```bash
   docker build -t jenkins-docker jenkins/
```

2. Start it:

```bash
   docker run -d --name jenkins --restart unless-stopped --group-add 984 -p 8080:8080 -p 50000:50000 -v jenkins_home:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock jenkins-docker
```

   Note: 984 is the docker group id on my machine. Find yours with: `getent group docker`

3. Open http://localhost:8080

## How the pipeline works

- lint: runs ruff inside a python:3.12 container.
- test: runs pytest on Python 3.11 and 3.12 in parallel (matrix).
- db-check: starts a Postgres container named `pg-check-<BUILD_NUMBER>` (unique per build), waits for it with a 60 second timeout, creates a table with 2 columns and 5 rows, and checks the count is 5.
- post: always removes the Postgres container, even if the build fails.

## Connect Jenkins to GitHub

1. Create a fine-grained GitHub token limited to this repo:
   - Contents: Read-only
   - Pull requests: Read-only
   - Commit statuses: Read and write (so Jenkins can show the build result on the PR)
2. In Jenkins, go to Manage Jenkins, then Credentials, and add the token as "Username with password" (username is your GitHub username, password is the token). Never put the token in the Jenkinsfile or the repo.
3. Create a Multibranch Pipeline job and add a GitHub branch source with that credential and the repo URL.
4. Under Scan Repository Triggers, enable "Periodically if not otherwise run" and set 5 minutes.

Jenkins scans the repo every 5 minutes (polling), builds every branch and pull request that has a Jenkinsfile, and reports the result on the PR.

## Webhook

GitHub cannot reach `localhost:8080`, so this setup uses polling. On a server with a public URL, add a webhook in the repo settings (Settings, Webhooks) with payload URL `https://<your-jenkins>/github-webhook/` and content type `application/json`. Builds then start on every push and pull request without waiting for the scan.