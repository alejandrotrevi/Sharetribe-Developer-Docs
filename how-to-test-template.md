```markdown
---
title: "Testing and Continuous Integration"
source: "provided"
extracted_date: "2025-04-24"
summary: "This guide describes how to use the test setup in the Sharetribe Web Template and how to enable Circle CI for continuous integration."
---

- [Table of Contents](#table-of-contents)
  - [Testing and snapshot tests](#testing-and-snapshot-tests)
  - [Running the tests](#running-the-tests)
  - [Extending tests](#extending-tests)
  - [Jest](#jest)
  - [Continuous integration](#continuous-integration)
  - [Code formatting](#code-formatting)
  - [Build](#build)
  - [Tests](#tests)
  - [Security audit](#security-audit)

# Testing and Continuous Integration
This guide describes how to use the test setup in the Sharetribe Web Template and how to enable Circle CI for continuous integration.

## Testing and Snapshot Tests
The Sharetribe Web Template uses the [Jest testing framework](https://jestjs.io/) as its test runner. The setup uses is based on how testing is implemented in `create-react-app`. For reference, see the testing section in the [create-react-app documentation](https://create-react-app.dev/docs/running-tests).

### Running the Tests
To start the test watcher that automatically updates when files change, run:

```bash
yarn test
```

If you want to run the tests once and not start the watcher, run:

```bash
CI=true yarn test
```

Note that this also runs the linter.

> **Information:** In some environments, the alternative test watcher doesn't always work and it can die unexpectedly. If that happens to you, you might want to install [Watchman](https://facebook.github.io/watchman/docs/install/). Read more about [this issue](https://github.com/facebook/create-react-app/issues/871).

### Extending Tests
Most tests included in the template are [snapshot tests](https://jestjs.io/docs/snapshot-testing).

> “A typical snapshot test case renders a UI component, takes a snapshot, then compares it to a reference snapshot file stored alongside the test. The test will fail if the two snapshots do not match: either the change is unexpected, or the reference snapshot needs to be updated to the new version of the UI component.”

A failing snapshot can be updated through the [Jest watch mode](https://jestjs.io/docs/snapshot-testing#interactive-snapshot-mode). Even though most tests in the template are UI-focused, [some tests](https://github.com/sharetribe/web-template/blob/main/server/api-util/currency.test.js) are written with unit-testing in mind.

Test files can be found next to the code they are testing and can be identified by a `.test.js` suffix. Snapshots are located in a nested folder `__snapshots__`.

```text
├── ManageListingCard.example.js
├── ManageListingCard.js
├── ManageListingCard.module.css
├── ManageListingCard.test.js
├── MenuIcon.js
├── Overlay.js
├── Overlay.module.css
└── __snapshots__
    └── ManageListingCard.test.js.snap
```

The template does not include full test coverage; it is intended to be extended and customized, which quickly renders the default tests obsolete. The default tests are there to provide a good starting point for writing tests.

### Jest
[Jest](https://jestjs.io/) is a JavaScript test runner that runs tests in a Node environment. The test runner accesses the DOM using the [jsdom](https://github.com/jsdom/jsdom) library. As the tests are run in a Node environment, they are not exact portrayals of real browser behaviour, providing good iteration speed and balance between accuracy, simplicity, and performance.

Jest provides detailed documentation on their testing framework. If you are interested in extending the in-built tests provided with the template, the following guides can provide additional insight:
- [Getting Started](https://jestjs.io/docs/en/getting-started)
- [Tutorial - React](https://jestjs.io/docs/en/tutorial-react)
- [Tutorial - Async](https://jestjs.io/docs/en/tutorial-async)
- [Snapshot Testing](https://jestjs.io/blog/2016/07/27/jest-14.html) blog post
- [API Reference](https://jestjs.io/docs/en/api) lists the global environment with available functions and assertion matchers.

## Continuous Integration
The Sharetribe Web Template provides a configuration to use [CircleCI](https://circleci.com/) as a continuous integration server to run tests and other scripts. Continuous integration prevents deploying changes that break tests or fail audits.

You can use the [.circleci/config.yml](https://github.com/sharetribe/web-template/blob/main/.circleci/config.yml) file to configure CircleCI.

Follow the [Setting up Your Build on CircleCI](https://circleci.com/docs/guides/getting-started/getting-started/) instructions in the CircleCI documentation to enable Circle CI.

### Code Formatting
```bash
yarn run format-ci
```
This command fails if there are changes in formatting that are not committed. Run `yarn run format` to format the code and get rid of the error.

### Build
```bash
yarn run build
```
This command ensures that the build passes.

### Tests
```bash
yarn run test-ci
```
This command runs the tests.

### Security Audit
```bash
yarn run audit
```
This command runs the security audit using `yarn audit --json` and checks the returned JSON against vulnerability exceptions defined in the `.auditrc` file at the project root. The audit checks for installed packages with known vulnerabilities and warns about those.

The script outputs information about the dependency path that added the package. If that information is not enough, `yarn why package-name` can be used to get more detailed information about why the package is installed.

## Can't find what you're looking for?
Reach out to our support team by [email](mailto:hello@sharetribe.com), or consult the Sharetribe Developer Community at [Sharetribe Developers Slack Channel](https://www.sharetribe.com/dev-slack). You can also contact our support team through the chat widget in Console.
```