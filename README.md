# upload-status-badge

GitHub action to create a status badge with the result of a command, and upload
it to the specified gist id.

## Usage

See [action.yml](action.yml).

## Example Workflow

```yaml
name: Deploy

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Install dependencies
        id: install-dependencies
        run: npm ci --production

      - name: Upload badge with result of install dependencies
        uses: colinparsonsme/upload-status-badge@v1
        with:
          gist-secret: ${{ SECRETS.gist-secret }}
          gist-id: dependencies-install-gist
          badge-label: Dependencies install
          outcome: ${{ steps.install-dependencies.outcome }}

      # more deployment steps here...
```

## License

See the [License](LICENSE).

## Contributing

See the [Contributing Guidelines](CONTRIBUTING.md).

### Contributor Code of Conduct

See the [Code of Conduct](CODE-OF-CONDUCT.md).

## Contact

If you're interested in getting in touch outside of this project, check out
[colinparsons.com](https://colinparsons.com).
