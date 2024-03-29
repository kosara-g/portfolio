# Vale CLI tutorial

> **Before you begin**: this tutorial assumes you are using *macOS*.

<!-- TOC depthfrom:2 -->

- [Scope of this tutorial](#scope-of-this-tutorial)
- [About Vale CLI](#about-vale-cli)
- [Installation](#installation)
    - [Configure Vale](#configure-vale)
- [Run Vale](#run-vale)
- [Further reading](#further-reading)

<!-- /TOC -->

---

## Scope of this tutorial

This overview covers what Vale is, how to install and set it up on the command line, and how to use it to lint documentation.

The instructions use the default Vale style and the Google style. Feel free to try it out with other styles.

## About Vale CLI

The *Vale CLI* is a command-line interface that allows you to run Vale on your local machine.

## Installation

The recommended method for installing Vale is through a package manager, in this example, [Homebrew](https://formulae.brew.sh/formula/vale).

1. Open the Terminal app.
2. Run this command in the terminal:

```sh
$ brew install vale
```

3. Verify that the installation succeeded by running this command to view the current version:
    
```sh
$ vale --version
```

### Configure Vale

1. You need to create the project structure for the Vale configuration files. In this example, the top-level directory is `/vale`.

The `/styles` subdirectory is where you store the different rule packages, or *styles*. The default style is **Vale**.

`.vale.ini` is the configuration file, where you configure which styles and which severity settings you want to apply.

```sh
$ mkdir vale && cd vale
$ mkdir styles
$ touch .vale.ini
```

2. Add your configuration to the `.vale.ini` file. You can use the [Vale configuration file generator](https://vale.sh/generator) to pre-fill the file for you.

```
StylesPath = styles
Packages = Google
MinAlertLevel = suggestion
[*.md]
BasedOnStyles = Vale, Google
```

`StylesPath` sets the directory where Vale can find any external packages. In this example, that's the `/styles/` directory.

`MinAlertLevel` determines which issues Vale reports based on their severity - they can be errors, warnings, or suggestions. Setting this parameter to `suggestion` means that the report includes **all** issues, down to the suggestions. To see just the errors, you can set it to `error`.

The `[*.md]` parameter determines which files to lint, in this example, all Markdown files. To lint any file type, use `[*]` instead.

`BasedOnStyles` determines the styles to apply. In this case, the default Vale rules and the [Google style guide](https://developers.google.com/style).

3. Download the list of packages you specified in `Packages`. Run this command in the terminal

```sh
$ vale sync
```

You need to do this once during setup and anytime there is a new package release.

Once the packages appear in your `StylesPath` directory, your project structure should look like this:

```
vale
├── .vale.ini
└── styles
    └── Google
```

## Run Vale

You can lint single files or entire directories. You **must** run Vale from the **same** directory as your `.vale.ini` configuration file.

To lint a README file in another directory, you can run Vale like this:

```sh
$ cd vale
$ vale ../repos/portfolio/README.md
```

To lint an entire project from the same directory as Vale, you can run the following command:

```sh
$ cd vale
$ vale .
```

The output from the linter includes information on which package found the issue, which line and character position you should look at, and how to fix it.

---

## Further reading

You can find the complete Vale CLI reference in the [official Vale documentation](https://vale.sh/docs/).

To see all available Vale packages, visit [Valeʼs Package Hub](https://vale.sh/hub/). 
