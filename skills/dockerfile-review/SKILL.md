---
name: dockerfile-review
description: Reviews Dockerfiles and container build configuration for security, image size, build cache efficiency, reproducibility, and runtime correctness. Use when the user shares a Dockerfile or .dockerignore, asks why an image is large or slow to build, or wants a container hardened before deployment.
license: MIT
---

# Dockerfile Review

Review a Dockerfile line by line and produce prioritized, actionable findings with a
corrected Dockerfile when changes are substantial.

## Inputs to collect

- The Dockerfile and `.dockerignore` (if `.dockerignore` is missing, that is a finding).
- The application language and how it is started.
- Whether the image runs in production, CI, or local development only.

If any of these are unknown, state the assumption you are making.

## Checklist

### Security (blocker / major)

- **Runs as root.** The final stage should switch to a non-root user (`USER app` or a
  numeric UID). Files the app needs to write must be owned by that user.
- **Secrets in the image.** No API keys, tokens, `.env` files, or private keys copied in
  or passed through `ARG`/`ENV`. Build-time secrets should use the builder's secret mount
  feature, not build arguments, because build arguments are recorded in image history.
- **Unpinned or untrusted base images.** Base images should come from an official or
  internal trusted registry and be pinned to a specific version tag; pin by digest for
  production images.
- **Unnecessary tools in the runtime image.** Compilers, package managers, `curl`, and
  shells in the final stage increase attack surface. Use a multi-stage build and a slim or
  distroless runtime base where practical.
- **Remote downloads without verification.** `curl | sh` or downloading binaries without
  checksum verification.

### Image size (major / minor)

- Use multi-stage builds: compile or install dependencies in a builder stage, copy only
  artifacts into the runtime stage.
- Combine package installation and cache cleanup in the same `RUN` layer
  (for example `apt-get install --no-install-recommends ... && rm -rf /var/lib/apt/lists/*`).
- Disable package manager caches (`pip install --no-cache-dir`, `npm ci` followed by
  removing dev dependencies or using a production install).
- Ensure `.dockerignore` excludes `.git`, `node_modules`, virtual environments, build
  output, test data, and local environment files.

### Build cache efficiency (minor)

- Copy dependency manifests (`package.json` and lockfile, `requirements.txt`,
  `pyproject.toml` and lockfile, `go.mod`/`go.sum`) and install dependencies **before**
  copying the rest of the source, so code changes do not invalidate the dependency layer.
- Order instructions from least to most frequently changing.
- Avoid `COPY . .` early in the file.

### Reproducibility (minor)

- Use lockfiles and deterministic install commands (`npm ci`, `pip install -r` with
  pinned versions, `go mod download`).
- Pin versions of OS packages that matter for behavior.
- Avoid `latest` tags anywhere.

### Runtime correctness (major / minor)

- Use exec form for `CMD`/`ENTRYPOINT` (`["node", "server.js"]`) so the process receives
  signals and shuts down gracefully. Shell form wraps the process in a shell that does not
  forward signals.
- Ensure only one main process, or use a minimal init (`--init` or `tini`) if the app spawns
  children.
- Declare `EXPOSE` for documentation and add a `HEALTHCHECK` when the orchestrator does not
  provide its own health probes.
- Set `WORKDIR` explicitly instead of `cd` in `RUN` instructions.
- Language-specific settings: for Python, `PYTHONDONTWRITEBYTECODE=1` and
  `PYTHONUNBUFFERED=1`; for Node.js, `NODE_ENV=production`.

## Output format

```
## Summary
<image purpose, overall risk level, biggest win>

## Findings
| # | Severity | Line | Issue | Fix |
|---|----------|------|-------|-----|

## Suggested Dockerfile
<full corrected Dockerfile, only if there are 3+ findings or any blocker>

## Expected impact
<size, build time, and security improvements, stated as estimates>
```

Do not claim exact size reductions without building the image; label estimates as
estimates.
