---
layout: post
title: Use Spectral as OAS linter in your Web API development lifecycle
css: ['/assets/css/style.css']
tags: [Spectral, OAS linter]
---

Whenever you create OpenAPI Specification (OAS) description documents, either generated from code or hand-crafted, for your Web APIs, it is a good practice to validate them with a linter. Not only can you assure that your description documents are OAS compliant, you can also increase readability and maintainability of your OAS description documents and by adhering to the same rules you can ensure that they have the same look-and-feel.

In my development workflow, I use [Stoplight's Spectral][1] for the following reasons:

- it can be integrated into Visual Studio Code, my preferred editor for OAS description documents. Spectral can be configured to give feedback while you are typing
- it has a built-in ruleset to get you started, but can be customized and extended with custom rules
- it has as a Command Line Interface (CLI), making it possible to integrate Spectral into a CI/CD pipeline
- it has a JavaScript API. This is essential if you want to write tests to validate the working of your custom rules

## Setup Spectral Visual Studio Code extension

If you use Visual Studio Code (VS Code), you can integrate Spectral by installing its VSCode extension either via the [Marketplace website][2] or via VS Code's Extensions View (<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>X</kbd>).

To experience how Spectral works with VS Code, create a folder (to follow along, name it `oas`) to hold your OAS description documents, and create a file named `minimal-oas3.yaml` in the folder. In the editor window, enter the following:

``` yaml
openapi: 3.0.3
```

You will now see that the entered text is underlined with a squiggly line, which indicates there are issues. To see the issues, hover your mouse over the text or open the **problems** panel.

![Spectral VS Code Extension](/img/spectral-vscode.png)

## Setup Spectral CLI for your Web API development project

The Spectral CLI is a node module. To be able to use it, [Node.js][3] must be installed. When this prerequisite is met, install Spectral on your machine using the following statement:

``` bash
npm install -g @stoplight/spectral
```

Spectral can also be installed as a development dependency for your Web API project. To be able to do this, a `package.json` file must be created for your project. Use the following statement if one must be created:

``` bash
# create a package.json file for your project to keep track of the dependent node modules
npm init -y
```

Install Spectral as a development dependency with the following statement:

``` bash
# install Spectral as development dependency
npm install --save-dev @stoplight/spectral
```

Lint the `minimal-oas3.yaml` file created in Visual Studio Code with the following statement:

``` bash
spectral lint ./oas/minimal-oas3.yaml
```

Spectral will return with Exit Status 1 and the same error and warning messages as in Visual Studio Code

``` output
 1:1  warning  info-contact      Info object should contain `contact` object.
 1:1  warning  info-description  OpenAPI object info `description` must be present and non-empty string.
 1:1  warning  oas3-api-servers  OpenAPI `servers` must be present and non-empty array.
 1:1    error  oas3-schema       Object should have required property `info`.
 1:1  warning  openapi-tags      OpenAPI object should have non-empty `tags` array.

✖ 5 problems (1 error, 4 warnings, 0 infos, 0 hints)
```

To solve the error, append the following lines to the `minimal-oas3.yaml` file:

``` yaml
info:
  title: Minimal OAS 3.0.x description document
  version: 1.0.0 - Alfa
paths: {}
```

## Create a ruleset file for your project

To override Spectral's built-in ruleset and/or to extend it with your own rules, you have to create a ruleset file for the project. Name the file `.spectral.yml` and put it in the project's root folder if you want to use the default `Ruleset file` setting of both Spectral's VS Code extension and CLI.

Add the following lines to use Spectral's built-in ruleset as baseline for your own ruleset:

``` yaml
extends:
- spectral:oas
```

To suppress the current warnings for the `minimal-oas3.yaml` file in both Visual Studio Code and the CLI, append the following lines to the `.spectral.yml` file:

``` yaml
rules:
  oas3-api-servers: off
  openapi-tags: off
  info-contact: off
  info-description: off
```

Remove these suppressions so that we can verify that the warnings will show up in GitHub after the next step is implemented.

## Lint OAS3 description documents when they are pushed to GitHub

If you use GitHub as your git provider, a GitHub Actions workflow can be created to lint the OAS3 description documents whenever changes to these documents are pushed to GitHub. To define the lint workflow for our project, create a file named `lint-oas3.yaml` in the `.github/workflows` directory and add the following lines:

``` yaml
name: lint OAS3 description documents with Spectral

on:
  push:
    paths:
      # lint all OAS3 description documents if changes to the following files are pushed to remote
      - ./oas/**/*.yaml
      - .github/workflows/lint-oas3.yaml
      - .spectral.yaml
  workflow_dispatch:
      # enable 'trigger workflow manually'

jobs:
  spectral-lint:
    runs-on: ubuntu-latest
    steps:
      # checkout the repository, so the next steps can access the files in the repository
      - uses: actions/checkout@v2
      # install node.js required to run Spectral
      - uses: actions/setup-node@v1
        with:
          node-version: '12'
      # install Spectral
      - run: npm install
      # run the oas:lint script
      - run: npm run oas:lint
```

Also, create a script named `oas:lint` in the `package.json` file to simplify the invocation of Spectral:

``` json
{
    ...
    "scripts": {
        ...
        "oas:lint": "spectral lint ./oas/*"
        ... 
    }
    ...
}
```

Push the files we have created up to now to GitHub, then navigate to the website of the GitHub repository and open the **Actions** page to get an overview of the runs of the workflows of your GitHub repository.

![GitHub Actions workflows](/img/spectral-github-actions.png)

Click on a workflow run to see the summary of the run

![GitHub Actions workflow run summary](/img/spectral-github-actions-summary.png)

In the run summary, the same Spectral warnings are shown as in VS Code and in Spectral CLI.

## Lint OAS description documents before a commit

If you want to fail fast to learn fast, you don't want to wait until your commits are pushed to the remote repository to find out that your changes contains errors or do not comply. Luckily there is tooling to help prevent bad commits. With [Husky][4], you can run a pre-commit script to lint OAS description documents every time a commit is performed. To setup Husky:

``` bash
# install husky as development dependency
npm install --save-dev husky
```

Add the following lines to the `package.json` file to configure Husky:

``` json
{
    ...
    "scripts": {
        ...
        "oas:lint-warning-as-errors": "spectral lint -F warn ./oas/*"
        ... 
    },
    ...
    "husky": {
        "hooks": {
            "pre-commit": "npm run oas:lint-warning-as-errors"
        }
    }
    ...
}
```

The script `oas:lint-warning-as-errors` is added to verify the correct setup of Husky. This script runs Spectral with the **fail severity** option set to `warn` (-F warn) so that Spectral would return a failure exit code when there are warnings found for the linted OAS description documents. This will trigger Husky to cancel the commit.

Change the **pre-commit** script to `npm run oas:lint` and stage this change to commit your Husky configuration.

## Create custom rules

Spectral provides a Domain Specific Language for writing custom rules. Checkout Spectral's [Custom Rulesets][5] documentation to learn more about the language syntax. In essence, a rule consists of:

- a name to identify the rule
- a `given` property that consists of JSONPath expressions that is used to find the elements the rule must be applied to
- a `then` property that indicates the function that must be applied on the found elements

Rules must be added under the `rules` property of a ruleset. Below is an example of a rule. This rule checks that the name of every schema definition (found with: $.components.schemas.*~) is written in pascal case (first letter of each word in a compound word is capitalized).

``` yaml
rules:
  schema-names-pascal-case:
    description: Schema names MUST be written in PascalCase
    message: "component '{{property}}' {{error}}"
    severity: error
    given: '$.components.schemas.*~'
    then:
      function: casing
      functionOptions:
        type: pascal
```

The following online tools are indispensable when writing custom rules:

- [yaml to json converter][6] to transform (part of) a OAS description document to JSON format
- [JSONPath Online Evaluator][7] to test JSONPath expressions on the transformed OAS description documents

When you have implemented a new rule, it is important to test it extensively to minimize the chance of getting false positives (the rule finds an issue while there is no issue) and false negatives (the rule doesn't find an issue while there is an issue). Luckily Spectral exposes a JavaScript API, so it is possible to write code to test the implementation of a rule.

To kill two birds with one stone, we will write the tests with Gherkin. Even though, you can provide documentation via the description property of a rule, it is much more clearer to use examples to describe the intent of a rule. With Gherkin we can write these examples in natural language so that it is understandable for everyone.

Below is the content of the `schema-names-pascal-case` feature file where the working of the `schema-names-pascal-case` rule is described using examples or scenarios. The first scenario shows what a schema name written in pascal case looks like and the second scenario shows schema names that are not written in pascal case. In the feature file, you can also see the usage of Gherkin keywords like `Background` and `Scenario Outline` to minimize repetitions in scenario's. Check the [documentation of Gherkin][8] to learn more about the syntax

``` gherkin
Feature: Schema names Must be written in PascalCase

  Background:
    Given the rule 'schema-names-pascal-case'

  Scenario: Schema name is written in PascalCase
    Given the OAS3 description document
    """
    openapi: 3.0.3
    components:
      schemas:
        PascalCaseComponent:
          type: object
    """
    When the OAS3 description document is linted
    Then there should be no errors

  Scenario Outline: Schemas name is not written in PascalCase
    Given the OAS3 description document
    """
    openapi: 3.0.3
    components:
      schemas:
        <component name>:
          type: object
    """
    When the OAS description document is linted
    Then the error message should be
    """
    component '<component name>' must be pascal case
    """

    Examples:
      | component name       |
      | camelCaseComponent   |
      | snake_case_component |
      | kebab-case-component |
      | Mix-Case-Component   |
```

These specifications can be turned into executable specifications using [Cucumber Open][9]. The JavaScript version of Cucumber Open can be installed using the following statement:

``` bash
npm install --save-dev @cucumber/cucumber
```

After installation, configure cucumber by creating a file named `cucumber.js` in the project's root folder with the following lines:

``` javascript
module.exports = {
    default: '--publish-quiet'
}
```

Create a folder named `features` to store the feature files and create a sub-folder named `step-definitions` where the step definition files are stored. A step definition is the mapping of a Gherkin step with code that must be executed for the step. Below is the content of the `stepdefs.js` file with the step definitions for the steps used in the scenario's described above.

``` javascript
Given('the rule {string}', function(rule) {
    this.rule = rule;
});

Given('the OAS3 description document', function(oas3Document) {
    this.document = new Document(oas3Document, Parsers.Yaml);
});

When('the OAS description document is linted', async function() {
    const spectral = new Spectral();
    spectral.registerFormat('oas3', isOpenApiv3);
    await spectral.loadRuleset([
        join(__dirname, '../../spectral_rules/' + this.rule + '.yaml')
    ]);
    this.results = await spectral.run(this.document);
});

Then('there should be no errors', function() {
    this.results.should.have.lengthOf(0, JSON.stringify(this.results, null, "\t"));
});

Then('the error message should be', function(expected) {
    this.results.should.have.lengthOf(1, JSON.stringify(this.results, null, "\t"));
    this.results[0].code.should.equal(this.rule, JSON.stringify(this.results, null, "\t"));
    this.results[0].message.should.equal(expected, JSON.stringify(this.results, null, "\t"));
});
```

To ensure that the test results are correct, the rules must be tested in isolation. To achieve this, every rule is put in its own ruleset file. An additional advantage of this isolation is that you can create OAS description documents with only the properties that are needed to validate the rule. The ruleset files are stored in the `spectral_rules` folder.

Additionally, install the [Chai Assertion Library][10] to increase the expressiveness and readability of the step definitions code

``` bash
npm install --save-dev chai
```

Without Chai, the assertion `this.results.should.have.lengthOf(0, JSON.stringify(this.results, null, "\t"));` would have been written as `assert.equal(this.results.length, 0, JSON.stringify(this.results, null, "\t"))`.

To extend your ruleset with your new rule, add the path to the rule file below the `extends` property:

``` yaml
extends:
- spectral:oas
- ./spectral_rules/schema-names-pascal-case.yaml
```

## Conclusion

Spectral makes it easy to integrate an OAS linter in your Web API development workflow. With Spectral's DSL you can implement your own rules and you can automate validation of these rules using Spectral's JavaScript API. And if you write these tests with Gherkin, these tests can become the living documentation of your rules and ultimately the documentation of your API design style guide. Feel free to check my [GitHub repository][11] for more examples of feature files written to verify custom Spectral rules.

[1]: https://meta.stoplight.io/docs/spectral/README.md
[2]: https://marketplace.visualstudio.com/items?itemName=stoplight.spectral
[3]: https://nodejs.org
[4]: https://github.com/typicode/husky/tree/master
[5]: https://meta.stoplight.io/docs/spectral/docs/guides/4-custom-rulesets.md
[6]: https://onlineyamltools.com/convert-yaml-to-json
[7]: https://jsonpath.com/
[8]: https://cucumber.io/docs/gherkin/
[9]: https://cucumber.io/docs/cucumber/
[10]: https://www.chaijs.com/
[11]: https://github.com/MelvLee/spectral-playground