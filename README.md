# .github

Shared GitHub configuration for the public repositories of [@lrozewicz](https://github.com/lrozewicz):

- default community health files (`CONTRIBUTING.md`, `SECURITY.md`, issue and PR templates), used by every repo that does not have its own;
- reusable CI/CD workflows in `.github/workflows/`.

## Reusable workflows

| Workflow | Trigger in the app repo | What it does |
|---|---|---|
| `pr.yml` | `pull_request` | `docker build --target test`, runtime image build, Trivy (fails on fixable CRITICAL). No secrets, nothing is pushed. |
| `deploy.yml` | `push` to `dev` / `main` | Builds the image once per git tree (tag = `HEAD^{tree}`), pushes it to Harbor, scans it, tags it `dev-<tree>` / `prod-<tree>` and commits that tag to `platform-gitops`; Argo CD deploys it. On `main` an existing image of the same tree is promoted without a rebuild. |

Usage: see [`app-template`](https://github.com/lrozewicz/app-template) (`.github/workflows/ci.yml`).

App contract:

- `Dockerfile` with stages `test` (runs tests and lint; the build fails if they fail) and `runtime`;
- HTTP endpoint `/healthz`;
- runs as UID `10001` with a read-only root filesystem (only `/tmp` is writable).

Secrets required in the app repo: `HARBOR_USERNAME`, `HARBOR_PASSWORD` (Harbor robot account) and
`GITOPS_APP_PRIVATE_KEY` (GitHub App with write access to `platform-gitops` only).
They are set by the bootstrap script.
