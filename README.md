# restorm-action

Run a [Restorm](https://restorm.app) scenario in a GitHub Actions workflow.

```yaml
- uses: Monsieur-Dev/restorm-action@v1
  with:
    project: ./api.restorm
    scenario: Smoke tests
  env:
    RESTORM_TOKEN: ${{ secrets.RESTORM_TOKEN }}
```

The action is not published yet — this repository is the place it will live.
Until then, a pipeline can call the launcher directly:

```yaml
- run: npx restorm-cli --open ./api.restorm --run "Smoke tests" --headless
  env:
    RESTORM_TOKEN: ${{ secrets.RESTORM_TOKEN }}
```

Documentation: [headless execution and CI](https://restorm.app/en/docs/scenarios/execution-headless-ci/).
