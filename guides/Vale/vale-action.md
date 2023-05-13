# Vale GitHub action tutorial

Vale can be set up to lint documentation on GitHub automatically with the official [Vale action](https://github.com/errata-ai/vale-action).

<!-- TOC depthfrom:2 -->

- [Set up the workflow](#set-up-the-workflow)
    - [Target project structure](#target-project-structure)
    - [Create your Vale configuration](#create-your-vale-configuration)
    - [Create your workflow file](#create-your-workflow-file)
- [Test the workflow](#test-the-workflow)

<!-- /TOC -->

---

## Set up the workflow

This guide assumes youʼre already familiar with the basics of GitHub actions. If thatʼs not the case, check out [this overview of using workflows](https://docs.github.com/en/actions/using-workflows/about-workflows).

### Target project structure

At the end of this guide, your repository directory structure should look more or less like this:

Letʼs examine the setup process step by step.

```
my-repo
├── ...
├── .github
│   ├── ...
│   ├── styles
│   │       └── Vocab
│   │           └── my-domain
│   │               ├── accept.txt
│   │               └── reject.txt
│   └── workflows
│       └── lint_docs.yml
├── .vale.ini
└── ...
```

### Create your Vale configuration

Create a .vale.ini file at the root of your project and include the following configuration (you can modify it at any point):

```
StylesPath = .github/styles
MinAlertLevel = warning
Packages = Google, proselint, write-good
Vocab = my-domain
[*.md]
BasedOnStyles = Vale, Google, proselint, write-good
```

For more information on what the different settings mean, see the [Vale CLI guide](./vale-cli.md).

In this instance, my-domain is an arbitrary name to further categorize a set of vocabulary entries to be ignored/flagged by the linter.

Next, create this directory subtree `/styles/Vocab/my-domain` under the directory `.github`. Within `/my-domain` create two files: `accept.txt` for phrases/patterns to be ignored by the linter and `reject.txt` for phrases/patterns that will always be flagged as errors.

For more information on the syntax for these vocabulary files, see the `Vale documentation`.

### Create your workflow file

Assumptions:

- your repo has under 50 documentation files (in Markdown, plain text, or YAML)
- you work with feature branches and do not push directly to the default branch

Create the subdirectory `/workflows` under `.github`, if it doesnʼt already exist. Create your workflow file, in this example, `lint_docs.yml` under `/workflows` with the following content:


```yml
name: Vale
# Run this job whenever a Markdown file is changed
on:
  push:
    paths:
      - '**.md'
jobs:
  Vale:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout branch
        uses: actions/checkout@v3
      - name: Lint documentation
        uses: errata-ai/vale-action@reviewdog
        with:
          # Report results for every PR and push
          reporter: github-check
```

If your repository contains many documentation files, you should consider [only triggering the workflow whenever a file has changed](https://github.com/tj-actions/changed-files#usage).

## Test the workflow

Push your changes to a feature branch. You should see the workflow run under the *Actions* tab with the name you set in the workflow file, in this case, *Vale*.

Click on the latest run in the list to view the full logs. You should see two jobs: one main job created by you in the workflow file (in this example, ???) and one created by the workflow implicitly: ???.

The main job logs show Vale and the Vale packages being installed, as well as the raw output from the linter.

The second job invokes [reviewdog](https://github.com/reviewdog/reviewdog#readme), which reports the results as [annotations](https://github.blog/2018-12-14-introducing-check-runs-and-annotations/) in GitHubʼs UI.

To view the issues inline, follow the links to the issues from the reviewdog logs or simply check the *Files changed* tab in your pull request.
