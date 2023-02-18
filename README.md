# upload-status-badge

GitHub action to create a status badge with the result of a command, and upload
it to the specified gist id.

## Usage

See [action.yml](action.yml).

Before using this action, you must create a GitHub personal access token with
the `gist` scope, and add it to your repository secrets. If you use Dependabot,
you also have to add this secret to Dependabot's secrets (_Settings > Secrets >
Dependabot_). You can create this token at `github.com/settings/tokens`. You'll
pass this token as the `auth` input to this action.

For each badge you create, you need to:

1. Create a gist at `gist.github.com`
2. Get its ID (the long alphanumerical part of its URL, found at
   `https://gist.githubusercontent.com/<user>/<gist-id>`)
3. Pass the ID as the `gist-id` input to the action
4. Access the badge you create using `img.shields.io`'s `endpoint` api call with
   the gist's URL. That is, the URL for your desired badge svg will be
   `https://img.shields.io/endpoint?url=https://gist.githubusercontent.com/<user>/<gist-id>/raw/result.json`

testchangeb

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

      # the first way to use this action is to use "success" or "failure" as
      # the input to the badge
      - name: Upload badge with result of install dependencies
        uses: colinparsonsme/upload-status-badge@v0
        with:
          auth: ${{ secrets.GITHUB_GIST_TOKEN }}
          gist-id: dependencies-install-gist
          badge-label: Dependencies install
          outcome: ${{ steps.install-dependencies.outcome }}

      - name: Run tests
        id: tests
        run: |
          test_output=$(npm run test --json)

          passed_tests=$(echo $test_output | jq '.numPassedTests')
          total_tests=$(echo $test_output | jq '.numTotalTests')

          echo "passed-tests=$passed_tests" >> $GITHUB_OUTPUT
          echo "total-tests=$total_tests" >> $GITHUB_OUTPUT

      # the second way to use this action is to use a fraction, percentage,
      # or float as the input to the badge
      - name: Upload badge with result of tests
        uses: colinparsonsme/upload-status-badge@v0
        with:
          auth: ${{ secrets.GITHUB_GIST_TOKEN }}
          gist-id: tests-gist
          badge-label: Tests
          passing-results:
            ${{ steps.tests.outputs.passed-tests }} / ${{
            steps.tests.outputs.total-tests }}
            # this can be a fraction, percentage, float, or mathematical
            # expression indicating either the number or proportion of
            # passing results. here, we use it to indicate the proportion of
            # tests that are passing.
          passing-results-thresholds:
            '0.5, 1'
            # passing results thresholds for when to change the badge color
            # are optional, and can be left out. these can similarly be
            # fractions, percentages, floats, or mathematical expressions.

      - name: Build
        id: build
        run: |
          url=$(npm run build)
          echo "url=$url" >> $GITHUB_OUTPUT

      # the third way to use this action is to simply display a custom message
      # and color on the badge
      - name: Upload badge with result of deploy
        uses: colinparsonsme/upload-status-badge@v0
        with:
          auth: ${{ secrets.GIST_SECRET }}
          gist-id: build-gist
          badge-label: Build URL
          custom-message: ${{ steps.build.outputs.url }}
          custom-color: 'purple' # can be any color supported by shields.io
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
