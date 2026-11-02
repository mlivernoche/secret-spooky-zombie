# Development container

`devcontainer.json` pulls the prebuilt image from
`ghcr.io/mlivernoche/secret-spooky-zombie/devcontainer:latest`.
The image includes Node.js 24, pnpm, tsrs, Vite+, the general-purpose command-line
tools, Codex, Docker-in-Docker, and the VS Code settings and extensions declared in
`prebuild/devcontainer.json`. Dev Container metadata in the image carries these
settings and the Docker-in-Docker startup configuration to consumers.

## Publishing

The **Dev container image** GitHub Actions workflow builds changes to this directory
on pull requests. On the default branch, it publishes `latest` and `sha-<commit>`
tags to `ghcr.io/<owner>/<repository>/devcontainer`. It also supports manual runs
on the default branch and a weekly rebuild to refresh the base image and OS
packages. Both `linux/amd64` and `linux/arm64` are built.

The workflow uses the repository's `GITHUB_TOKEN` with `packages: write`; no
additional publishing secret is needed. Run it once before opening a new container
using the prebuilt configuration. New GHCR packages are private by default: make
the package public for anonymous pulls, or authenticate the host's Docker client
with credentials that have `read:packages` access.

When creating a repository from this template, update the `image` URL in
`devcontainer.json` to the new repository's lowercase GHCR path. The workflow
derives its destination automatically. A `sha-<commit>` tag can replace `latest`
when a fixed image is preferred.

## Changing or building the image locally

Edit `prebuild/Dockerfile` for OS packages and pinned pnpm, tsrs, and Vite+
versions. Edit `prebuild/devcontainer.json` for Features and editor defaults;
its adjacent `devcontainer-lock.json` pins Feature releases. Keep the pnpm version
aligned with the root `package.json` and Vite+ aligned with the workspace catalog.

Before the first publication, or to try changes without publishing, use the
Dev Container CLI from the repository root:

```bash
devcontainer build --workspace-folder . --config .devcontainer/prebuild/devcontainer.json
devcontainer up --workspace-folder . --config .devcontainer/prebuild/devcontainer.json
```

Alternatively, temporarily replace the `image` entry in the main
`devcontainer.json` with the contents of `prebuild/devcontainer.json`, changing
`build.dockerfile` to `prebuild/Dockerfile` and `build.context` to `prebuild`.
Then use **Dev Containers: Rebuild and Reopen in Container** in VS Code.

Source files and project dependencies are not baked into the image. After opening
the container, install the dependencies needed by your project from the repository
root. This template uses `vp install --frozen-lockfile`. Add a workspace lifecycle
command to the main `devcontainer.json` if your derived project should install
dependencies automatically.
