# Contributing

Thanks for your interest! Contributions of all sizes are welcome.

## Workflow

1. For anything bigger than a small fix, open an issue first so we can agree on the approach.
2. Fork the repository and create a branch from **`main`**.
3. Make your change, with tests where it makes sense.
4. Open a pull request against **`main`**.

Every merge to `main` is deployed to a staging environment. Production releases are made by the
maintainers by tagging a commit on `main` (`v1.2.3`).

## Checks

Every pull request runs automatically (for first-time contributors after a maintainer's approval):

- the `test` stage of the `Dockerfile` (tests and linters),
- a build of the runtime image,
- a vulnerability scan with Trivy (fixable critical vulnerabilities fail the build),
- CodeQL code scanning.

You can run the same checks locally:

```bash
docker build --target test .
docker build --target runtime -t app .
```

A PR can be merged when all checks pass and a maintainer has approved it. An automated AI review may
leave comments; it is advisory only.

## Guidelines

- Keep pull requests focused: one change per PR.
- Write clear commit messages (what and why).
- Do not commit secrets, credentials or personal data.
- Be respectful. Harassment of any kind is not tolerated.

By contributing you agree that your contribution is licensed under the license of the repository.
