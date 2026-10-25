# Decisions

## Jenkins runs in Docker
Why: on my laptop, Docker makes Jenkins easy to start, stop and rebuild, and it keeps port 8080 free of the old systemd service.
On a server: same image, but builds should run on a separate agent.

## Jenkins polls GitHub every 5 minutes
Why: GitHub cannot reach localhost:8080, so a webhook cannot work on my laptop. Polling lets Jenkins find new branches and pull requests by itself.
On a server: use a webhook (payload URL https://<your-jenkins>/github-webhook/) so builds start on every push without waiting for the scan.

## Required check is pr-head, not pr-merge
Why: Jenkins reports pr-head for the pull request build. pr-merge never arrives, so requiring it would lock the merge button forever.
On a server: if the job is set to build the merge with main, pr-merge can be required instead.

## Pull request required, approvals set to 0
Why: I am the only person on this repo, so I cannot approve my own PR. Requiring 1 approval would block every merge.
On a server: at least 1 approval, and dismiss stale approvals when new commits are pushed.
