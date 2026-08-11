# VCS

The version-control conventions this project follows: branches, commits, and the platform.

## Setup

- Main branch: `main` (the repo's only branch); `cd.yml` publishes on push to it.
- Platform: GitHub (`crocogiciel/docker-sbx-template`, per the CI/CD badge URLs in `README.md`). The `origin` remote uses the SSH host alias `github-perso`, not `github.com` directly — cloning/pushing needs that alias configured locally.
- Ticketing: none configured.

## Commits

- Short, imperative, capitalized subjects, not conventional-commits `type(scope):` format — see `git log`. Only a couple of commits so far, so treat this as a thin sample.
- PR-based: `CONTRIBUTING.md` expects a pull request for anything that changes AI behavior on this project; `GUIDELINES.md` sets a green `ci.yml` build as the merge bar.
