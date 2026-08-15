# restorm-action

Run a [Restorm](https://restorm.app) scenario in a GitHub Actions workflow, and
fail the job on anything but success.

```yaml
- uses: Monsieur-Dev/restorm-action@v1
  with:
    project: ./api.restorm
    scenario: Smoke tests
  env:
    RESTORM_TOKEN: ${{ secrets.RESTORM_TOKEN }}
```

The action installs Restorm through [`restorm-cli`](https://www.npmjs.com/package/restorm-cli)
(one checksum-verified download, cached between jobs via `actions/cache`), wraps
it in `xvfb` on Linux runners, runs your scenario headlessly and maps the CLI's
exit codes onto named job errors.

## Inputs

| Input | Required | Default | Description |
| --- | --- | --- | --- |
| `project` | yes | — | Path to the `.restorm` project file |
| `scenario` | yes | — | Scenario name, breadcrumb path (`Folder/Sub/Scenario`) or index path |
| `version` | no | the action's own pin | Restorm version to run. `@v1.2.3` runs Restorm 1.2.3; pass `latest` to always follow the newest release |
| `params` | no | — | Scenario parameters, one `Name=value` per line |
| `out` | no | — | Also write the run log to this file |
| `args` | no | — | Extra arguments passed to the CLI verbatim |
| `cache` | no | `true` | Cache the downloaded build between jobs. Set `false` on self-hosted runners |

## Outputs

| Output | Description |
| --- | --- |
| `exit-code` | `0` success · `1` scenario failed · `2` project/scenario not found · `3` not entitled |
| `log` | Path of the run log, when `out` was given |

## The token

A headless run authenticates with an organization token (`rstk_…`) read from
`RESTORM_TOKEN`. Create one inside Restorm — the account button in the title
bar, then *My CLI/CI tokens* — store it as a repository secret, and pass it as
an env var on the step. The action refuses to start without it, before anything
is downloaded.

→ [Accounts, sign-in and CLI/CI tokens](https://restorm.app/en/docs/entreprise/comptes-et-connexion/)
· [Headless execution and CI](https://restorm.app/en/docs/scenarios/execution-headless-ci/)

## Parameters

```yaml
- uses: Monsieur-Dev/restorm-action@v1
  with:
    project: ./api.restorm
    scenario: Health check
    params: |
      environment=staging
      retries=3
  env:
    RESTORM_TOKEN: ${{ secrets.RESTORM_TOKEN }}
```

Each line becomes a `--param Name=value` for the scenario's input prompt.
Secrets should NOT travel this way — expose them as env vars and use the
scenario's environment-variable secret source instead, so they never appear in
a process command line.
