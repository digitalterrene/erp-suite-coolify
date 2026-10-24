# Contributing

Thanks for helping. This project aims to be a dependable, well-documented way to run the ERP suite on Coolify.

## Ground rules

- Be respectful. Follow the [Code of Conduct](CODE_OF_CONDUCT.md).
- Never include secrets, real domains or customer data in issues, commits or logs.
- Keep changes small and focused. One concern per pull request.
- Pin and test. A change that affects the image is only done when you have built a new tag and deployed it.

## Workflow

1. Open an issue (or a Discussion) for anything non-trivial, so we agree on the approach first.
2. Fork the repository and create a branch such as `feat/add-lending-app` or `fix/healthcheck-timeout`.
3. Make your change. Update `README.md` or `docs/` when behaviour or variables change.
4. Add an entry under **Unreleased** in `CHANGELOG.md`.
5. Open a pull request using the template. CI must be green.

We use [Conventional Commits](https://www.conventionalcommits.org/) for commit messages, for example
`feat: add Lending app` or `docs: explain cPanel SMTP settings`.

## Testing your change

- Run **Build ERP suite image** from your fork with a new tag.
- Deploy that tag on a throwaway Coolify resource with fresh volumes.
- Confirm the site loads, `/crm`, `/helpdesk` and `/insights` open, and `bench version` lists the expected apps.
- Put the image tag and what you checked in the pull request.

## Proposing a new app

Open a feature request with: the repository URL, the branch compatible with Frappe v16, its license,
and the result of a test build. New apps raise build time, image size and upgrade work, so each one needs a clear use.

## Local checks

```bash
pip install yamllint
yamllint .
jq -e . apps.json
```

## License

By contributing, you agree that your contributions are licensed under the [MIT License](LICENSE).
