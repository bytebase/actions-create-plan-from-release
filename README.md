# About

Github action to create release from plan on Bytebase.

## Example

```yaml
on:
  push:
    branches:
      - main

jobs:
  bytebase-cicd:
    runs-on: ubuntu-latest
    env:
      BYTEBASE_URL: 'https://demo.bytebase.com'
      BYTEBASE_PROJECT: 'projects/example'
      BYTEBASE_SERVICE_ACCOUNT: 'demo@service.bytebase.com'
      BYTEBASE_TARGETS: 'instances/mysql1/databases/db1,instances/mysql1/databases/db2'
    name: Bytebase cicd
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Login to Bytebase
        id: login
        uses: bytebase/login-action@main
        with:
          url: ${{ env.BYTEBASE_URL }}
          service-account: ${{ env.BYTEBASE_SERVICE_ACCOUNT }}
          service-account-key: ${{ secrets.BYTEBASE_PASSWORD }}
      - name: Create release
        id: create_release
        uses: bytebase/actions-create-release@main
        with:
          url: ${{ env.BYTEBASE_URL }}
          token: ${{ steps.login.outputs.token }}
          project: ${{ env.BYTEBASE_PROJECT }}
          file-pattern: 'migrations/*.sql'
      - name: Create plan
        id: create_plan
        uses: bytebase/actions-create-plan-from-release@main
        with:
          url: ${{ env.BYTEBASE_URL }}
          token: ${{ steps.login.outputs.token }}
          project: ${{ env.BYTEBASE_PROJECT }}
          release: ${{ steps.create_release.outputs.release }}
          targets: ${{ env.BYTEBASE_TARGETS }}
```
