# Static code analysis to catch bugs before you run the code (_PhpStan_)

![PHPStan logo](../pic/phpstan-logo.png)

## Why?

TODO

## Installing

TODO

## Creating a configuration file

TODO

An example `phpstan.neon` config file.

```yml
parameters:
    level: 9
    phpVersion: 70300
    editorUrl: 'vscode://file/%%file%%:%%line%%'
    tmpDir: .tools/phpstan
    reportUnmatchedIgnoredErrors: false
    paths:
        - .
    excludePaths:
        - .git
        - .temp
        - .tools
        - .vscode
        - vendor
        - clash
        - codegolf
        - contest
    dynamicConstantNames:
        - DEBUG
        - PERF_LOG
```

Having this file in my project folder I can run `phpstan --verbose`

## Usage

TODO

## Useful links

* [PhpStan](https://phpstan.org/)
* [Psalm](https://psalm.dev/), another popular static analyzer. You must use `Composer` though.
* [PHP Mess Detector](https://phpmd.org/), another analyzer.
* [Clean Code PHP](https://github.com/jupeter/clean-code-php), a collection of principles on "producing readable, reusable, and refactorable software in PHP".
* [PHP The Right Way](https://phptherightway.com/), a very thorough quick reference for PHP best practices.

## Coming next

Some CodinGame-specific tools and sites by the community
