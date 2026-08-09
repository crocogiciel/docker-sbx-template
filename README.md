# Docker Sandboxes Templates
[![CI][ci-badge-url]][ci-workflow-url]
[![CD][cd-badge-url]][cd-workflow-url]

This repository contains Docker Sandboxes templates ready to use.

## Install a template

Docker Sandbox templates to run AI coding agents in isolated sandboxes.

### 1. Install Docker Sandboxes

- Follow the installation guide: [Get started with Docker Sandboxes](https://docs.docker.com/ai/sandboxes/get-started/).

### 2. Sign in to Docker

- Sign in to Docker:

```bash
sbx login
```

- This will open a browser window to sign in to Docker.
- Sign in using your Docker account or register a new account.

### 3. Configure the security policy

- Set the default security policy:

```bash
sbx policy set-default balanced
```

- Allow the Docker Sandboxes to access the GitHub Container Registry:

```bash
sbx policy allow network "ghcr.io"
```

### 4. Create a GitHub token

- Navigate to GitHub
- Navigate to [Settings > Personal access tokens](https://github.com/settings/tokens)
- Click `Generate new token (classic)`
- Choose a clear name (e.g. `Docker Sandbox`)
- Select the `read:packages` scope
- Click `Generate token`
- Copy the token 

For security reason it is recommended to add only the `read:packages` scope so your 
sandbox cannot do stupid stuff on your github repositories.

### 5. Configure access to GitHub Container Registry

- Configure Docker Sandboxes to allow access to the GitHub Container Registry:

```bash
echo "YOUR_TOKEN" | sbx secret set --registry ghcr.io --password-stdin
```

### 6. Create the sandbox

- Create the Docker Sandbox using our custom template:

```bash
sbx run -t ghcr.io/crocogiciel/<PACKAGE_NAME>:<AGENT> <SBX-AGENT>
```

Supported `<PACKAGE_NAME>`: [list of packages can be found here](https://github.com/crocogiciel?tab=packages) 

Supported `<AGENT>` values:
- `claude-code`

Supported `<SBX-AGENT>` [list of sbx agents](https://docs.docker.com/ai/sandboxes/agents/)

## Usage

### 1. Interactive mode

- Run the interactive mode:

```bash
sbx
```

- Select your Docker Sandbox using the arrow keys.
- Press `Enter` to connect.

### 2. Non-interactive mode

- List the existing Docker Sandboxes:

```bash
sbx ls
```

- Connect to an existing Docker Sandbox:

```bash
sbx run <sandbox-name> 
```

## Learn more

- [Docker Sandboxes - Getting started](https://docs.docker.com/ai/sandboxes/get-started/)
- [Docker Sandboxes - Templates](https://docs.docker.com/ai/sandboxes/customize/templates/)
- [Docker Sandboxes - CLI reference](https://docs.docker.com/reference/cli/sbx/)


[ci-badge-url]: https://github.com/crocogiciel/docker-sbx-template/actions/workflows/ci.yml/badge.svg
[ci-workflow-url]: https://github.com/crocogiciel/docker-sbx-template/actions/workflows/ci.yml
[cd-badge-url]: https://github.com/crocogiciel/docker-sbx-template/actions/workflows/cd.yml/badge.svg
[cd-workflow-url]: https://github.com/crocogiciel/docker-sbx-template/actions/workflows/cd.yml
