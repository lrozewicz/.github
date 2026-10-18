# .github

Shared GitHub configuration for the public repositories of [@lrozewicz](https://github.com/lrozewicz):

- default community health files (`CONTRIBUTING.md`, `SECURITY.md`, issue and PR templates), used by every repo that does not have its own;
- reusable CI/CD workflows in `.github/workflows/`.

## Reusable workflows

| Workflow | Trigger in the app repo | What it does |
|---|---|---|
| `pr.yml` | `pull_request` | `docker build --target test`, runtime image build, Trivy (fails on fixable CRITICAL). No secrets, nothing is pushed. |
| `deploy.yml` | `push` to `main` (dev), release tag `v*` (prod) | Builds the image once per git tree (tag = `HEAD^{tree}`), pushes it to Harbor, scans it, tags it `dev-<tree>` / `prod-<tree>` (plus the release tag) and commits that tag to `platform-gitops`; Argo CD deploys it. A release tag promotes the image already built from `main` without a rebuild; it must point to a commit on `main`. |
| `ai-review.yml` | `pull_request_target` | Advisory AI review: reads the PR diff through the GitHub API (never checks out or runs PR code), asks an OpenAI-compatible model (default `glm-5.3-flash`) and posts one comment, updated on later pushes. Skips drafts and bot PRs; first-time contributors need the `ai-review` label. Not a required check. |

Usage: see [`app-template`](https://github.com/lrozewicz/app-template) (`.github/workflows/ci.yml`).

App contract:

- `Dockerfile` with stages `test` (runs tests and lint; the build fails if they fail) and `runtime`;
- HTTP endpoint `/healthz`;
- runs as UID `10001` with a read-only root filesystem (only `/tmp` is writable).

Secrets required in the app repo: `HARBOR_USERNAME`, `HARBOR_PASSWORD` (Harbor robot account),
`GITOPS_APP_PRIVATE_KEY` (GitHub App with write access to `platform-gitops` only) and
`AI_REVIEW_API_KEY` (model API key for the AI review).
They are set by the bootstrap script.
