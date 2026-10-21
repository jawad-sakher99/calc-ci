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

Note: 984 is the docker group id on my machine. Find yours with: getent group docker
3. Open http://localhost:8080

## How the pipeline works

- lint: runs ruff inside a python:3.12 container.
- test: runs pytest on Python 3.11 and 3.12 in parallel (matrix).
- db-check: starts a Postgres container, creates a table with 2 columns and 5 rows, and checks the count is 5.
- post: always removes the Postgres container, even if the build fails.
Pull requests are built automatically by Jenkins.