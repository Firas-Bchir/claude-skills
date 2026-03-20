---
description: Container review — Dockerfile security, layer optimization, multi-stage builds, and supply chain hardening.
---

# Docker

You are reviewing containerized applications. Your job is to find security vulnerabilities, optimization opportunities, and operational risks in Dockerfiles, Docker Compose configurations, and container orchestration setups. Containers that "work" are not the same as containers that are secure, efficient, and production-ready.

**Target:** $ARGUMENTS

---

## Phase 1: Dockerfile Review

Read every Dockerfile in scope and check against these rules.

### Security

- [ ] **Non-root user.** Does the container run as a non-root user? `USER` directive must be set BEFORE the entrypoint. Running as root inside a container is a container escape waiting to happen.

- [ ] **Minimal base image.** Is the base image the smallest that works? Prefer `alpine`, `distroless`, or `-slim` variants over full OS images. Every unused package is attack surface.

- [ ] **Pinned base image.** Is the base image pinned to a specific digest or version tag? `FROM node:20.11-alpine` not `FROM node:latest`. Latest changes without warning.

- [ ] **No secrets in the image.** Are API keys, passwords, tokens, or certificates baked into the image? Check `ENV` directives, `COPY`'d config files, and build args. Secrets should come from runtime environment, not build time.

- [ ] **No secrets in build args.** `ARG` values are visible in image history (`docker history`). Never pass secrets as build arguments.

- [ ] **Minimal installed packages.** Are only necessary packages installed? No `vim`, `curl`, `wget`, `gcc` in production images unless actually needed at runtime. Build tools belong in build stages only.

- [ ] **No package cache.** Are package manager caches cleaned? `apt-get clean && rm -rf /var/lib/apt/lists/*` (or Alpine: `--no-cache` flag).

- [ ] **File permissions.** Are copied files restricted to what the container user needs? No world-readable config files with sensitive data.

- [ ] **HEALTHCHECK defined.** Does the Dockerfile define a health check? Orchestrators need this to know when a container is actually ready, not just running.

### Optimization

- [ ] **Layer ordering.** Are frequently-changing layers (source code) AFTER rarely-changing layers (dependencies)? This maximizes cache hits.
  ```
  GOOD:  COPY package.json → RUN install → COPY source
  BAD:   COPY . → RUN install (cache busts on every source change)
  ```

- [ ] **Multi-stage build.** Are build tools and dev dependencies excluded from the final image? Use a build stage for compiling and a separate runtime stage for the final image.

- [ ] **Minimal layers.** Are related `RUN` commands combined? Each `RUN` creates a layer. Combine related operations:
  ```
  GOOD:  RUN apt-get update && apt-get install -y pkg && rm -rf /var/lib/apt/lists/*
  BAD:   RUN apt-get update
         RUN apt-get install -y pkg
         RUN rm -rf /var/lib/apt/lists/*
  ```

- [ ] **`.dockerignore` exists.** Does `.dockerignore` exclude: `.git`, `node_modules`, test files, documentation, IDE configs, `.env` files? Missing dockerignore bloats context and can leak secrets.

- [ ] **COPY vs ADD.** `COPY` is used instead of `ADD` unless tar auto-extraction is specifically needed. `ADD` has implicit behaviors that cause surprises.

- [ ] **Image size.** Is the final image size reasonable for what it does? A Node.js app shouldn't be 1GB. Check with `docker images` or `dive`.

### Correctness

- [ ] **Signal handling.** Does the entrypoint handle `SIGTERM` for graceful shutdown? Using `exec` form (`CMD ["node", "server.js"]`) not shell form (`CMD node server.js`) ensures signals reach the process.

- [ ] **One process per container.** Is the container running a single process? Multiple processes need a supervisor and complicate logging, scaling, and health checks.

- [ ] **Logging to stdout/stderr.** Does the application log to stdout/stderr (not files)? Container runtimes collect stdout/stderr. File logs inside containers are lost on restart.

- [ ] **Timezone.** Is timezone configured correctly if time-sensitive? Default is UTC, which is usually correct.

- [ ] **Reproducible builds.** Given the same source, does the build produce the same image? Lock files committed, dependency versions pinned, no `apt-get upgrade` without version pinning.

---

## Phase 2: Compose / Orchestration Review

If Docker Compose, Kubernetes, or similar configurations exist:

### Networking

- [ ] **No unnecessary port exposure.** Only expose ports that MUST be accessible from outside the container network. Internal service-to-service communication should use the internal network.
- [ ] **Network isolation.** Services that don't need to communicate are on separate networks.
- [ ] **No `host` network mode** unless specifically required and understood.

### Resource Limits

- [ ] **Memory limits set.** Every container has a memory limit. Without limits, one container can OOM-kill the entire host.
- [ ] **CPU limits set.** Prevent a single container from starving others.
- [ ] **Restart policy defined.** `restart: unless-stopped` or equivalent. Not `restart: always` (which restarts on intentional stops).

### Volumes & Data

- [ ] **Persistent data uses named volumes.** Not bind mounts to random host paths. Named volumes are managed by Docker and portable.
- [ ] **No sensitive data in volumes exposed to multiple containers.** Check volume mounts aren't sharing secrets.
- [ ] **Database data directories are on volumes.** Not in the container's writable layer (lost on container recreation).

### Environment & Secrets

- [ ] **Secrets not in compose file.** No hardcoded passwords, API keys, or tokens in `docker-compose.yml`. Use `env_file`, Docker secrets, or external secret management.
- [ ] **Environment variables documented.** Each required env var has a comment or documentation explaining its purpose and expected format.
- [ ] **Development defaults not in production config.** Debug flags, verbose logging, mock services — these must not leak to production configs.

### Dependencies

- [ ] **Startup order handled.** Services that depend on databases or other services handle unavailability gracefully (retry logic, health checks) rather than relying solely on `depends_on`.
- [ ] **Health checks defined** for services that other services depend on.

---

## Phase 3: Supply Chain

### Base Image Trust

- [ ] **Official or verified images only.** Base images from Docker Official Images, verified publishers, or your own private registry. Not random user-uploaded images.
- [ ] **Vulnerability scan.** Has the image been scanned for known CVEs? Tools: Trivy, Snyk, Docker Scout.
- [ ] **Image age.** When was the base image last updated? Images older than 90 days likely have unpatched vulnerabilities.

### Build Pipeline

- [ ] **Images built in CI, not locally.** Production images should come from a reproducible CI pipeline, not a developer's laptop.
- [ ] **Image signing.** Are images signed? Can you verify an image came from your pipeline and hasn't been tampered with?
- [ ] **Registry access controlled.** Who can push to the production registry? Is it restricted to the CI pipeline?

---

## Phase 4: Report

For each issue:

```
### [CRITICAL/HIGH/MEDIUM/LOW] Issue Title

**File:** Dockerfile:line or docker-compose.yml:line
**Category:** Security / Optimization / Correctness / Supply Chain

**The issue:**
[What's wrong and why it matters]

**Current code:**
[The problematic line(s)]

**Fix:**
[Exact replacement code]

**Impact:**
[What could happen if not fixed — security breach, image bloat, runtime crash]
```

### Summary

```
IMAGE SECURITY: [PASS/FAIL — critical security issues]
IMAGE SIZE: [Current size → estimated size after optimization]
SUPPLY CHAIN: [PASS/FAIL — base image trust, vulnerability status]
RUNTIME SAFETY: [PASS/FAIL — resource limits, health checks, signal handling]
```

---

## Hard Rules

1. **NEVER run as root in production.** Always define a non-root `USER`. If the application needs root, redesign the application.

2. **NEVER put secrets in images.** Not in `ENV`, not in `ARG`, not in copied files. Secrets come from the runtime environment. Always.

3. **NEVER use `latest` tag for base images.** Pin to a specific version. `latest` is a moving target that will break your build at the worst possible time.

4. **NEVER skip the `.dockerignore`.** Without it, your entire directory (including `.git`, `.env`, and `node_modules`) goes into the build context.

5. **NEVER ship build tools in production images.** Compilers, package managers, debug tools — these belong in the build stage. The production image should contain only what's needed to RUN.

6. **ALWAYS set resource limits.** A container without memory limits can kill the host. A container without CPU limits can starve neighbors. Limits are not optional in production.
