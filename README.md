[![Code Grade](https://www.code-inspector.com/project/183/status/svg)](https://www.code-inspector.com/public/project/183/Code%20Inspector%20CI%20Tools/dashboard)
[![Build Status](https://travis-ci.org/codeinspectorio/citool.svg?branch=master)](https://travis-ci.org/codeinspectorio/citool)


# Code-Inspector Continuous Integration Tool


These are various Python tools used to use the [code-inspector](https://www.code-inspector.com)
engine in a continuous integration pipeline and compare analysis.

The following programs are being included:

 * `code-inspector-project`: get metrics about a project
 * `code-inspector-compare`: compare a project metrics against another projects or branches


## Build

You can build and try the program using the following command:

```bash
python setup.py install --user
```

You will then find binaries in `$HOME/.local/bin`, for example `$HOME/.local/bin/code-inspector-compare`.

## Usage

You need to set your API keys with environment variables:

 * `CODE_INSPECTOR_ACCESS_KEY`: your access key API generated from your project preferences
 * `CODE_INSPECTOR_SECRET_KEY`: your secret key API generated from your project preferences

On a terminal, you would set them up like this:
```bash
export CODE_INSPECTOR_ACCESS_KEY=<INSERT-YOUR-API-ACCESS-KEY-HERE>
export CODE_INSPECTOR_SECRET_KEY=<INSERT-YOUR-API-SECRET-KEY-HERE>
```

### Project information tool

Get general information about a project.

Invoke the tool as follow:

```bash
code-inspector-project -p <PROJECT_NAME>
```

For example:

```bash
code-inspector-project -p "mergify integration"
```

This is an example of the execution results:

```
{
    "lastAnalysis": {
        "status": "Done", 
        "summary": {
            "duplicates": 0, 
            "duplicated_lines": 0, 
            "violations": 2
        }
    }, 
    "id": 173, 
    "name": "mergify integration"
}
```


### Compare tool

The compare tool is used to compare a project with another repository. 
Invoke the tool as follow:

```bash
code-inspector-compare -p "mergify integration" --kind <REPOSITORY_KIND> --url <URL_TO_OTHER_REPOSITORY> --target-branch=<BRANCH> --target-revision=<REVISION>
```

This command will compare the project `mergify integration` on code inspector with the project located
at the URL `<URL_TO_OTHER_REPOSITORY>`.

When the tool successfully executes, it reports the execution in a JSON object.
This object shows you the number of violations, duplicates so that developers can
reuse this later.
There is an example of the JSON object - as follow:

```
{
    "status": "Done", 
    "targetAnalysis": {
        "status": "Done", 
        "summary": {
            "duplicates": 0, 
            "violations": 2
        }
    }, 
    "id": 53, 
    "sourceAnalysis": {
        "status": "Done", 
        "summary": {
            "duplicates": 0, 
            "violations": 2
        }
    }
}
```

These are the explanation for the other parameters:
 * `kind`: kind of repository used for the target. Can be `Git`, `Github`, `Gitlab` or `Bitbucket`. Values are explained below. This parameter is **REQUIRED**.
 * `URL`: URL of the repository. This parameter is **REQUIRED**.
 * `target-revision`: revision of the target repository. This parameter is **OPTIONAL**
 * `target-branch`: branch of the target repository. This parameter is **OPTIONAL**


The `kind` argument is used to define how the target repository will be fetched.
Based on the value, we use credentials from the authenticated user to clone the repository.
For example, if you specify `Github`, the target repository will be cloned using the github credentials
of the authenticated user. If the user does not have any registered credentials on [Code Inspector](https://www.code-inspector.com),
analysis will fail.

These are the valid values for the `kind` argument:

 * `Github`: use the Github credentials of the user making the compare request to clone the target repository
 * `Git`: do not use any credentials to clone the target repository. Used to clone public repository that does not need authentication.
 * `Gitlab`: use the Gitlab credentials of the user making the compare request to clone the target repository
 * `Bitbucket`: use the Bitbucket credentials of the user making the compare request to clone the target repository


These are the potential return code:
 * `0`: everything is great and the target analysis does not have more violations or duplicates
 * `1`: error during the analysis
 * `2`: cannot find the project (specified with the `-p` option)
 * `3`: cannot start a new comparison
 * `5`: the target analysis has more violations than the source
 * `6`: the target analysis has more duplicates than the source


## About Code Inspector

[Code Inspector](https://www.code-inspector.com) is a software analysis platform to manage and mitigate
technical debt. It offers plans that caters to all developers. From our basic free plan for your personal project
to the gold plan (for companies with multiples private projects), [code-inspector](https://www.code-inspector.com) detects coding issues
and helps you keep your project afloat.
