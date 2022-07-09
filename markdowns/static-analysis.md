# Static code analysis to catch bugs before you run the code (_PhpStan_)

![PHPStan logo](../pic/phpstan-logo.png)

## Why?

TODO

## Installing

TODO

## Custom configfile

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

## Usage

TODO

## Useful links

* [PhpStan](https://phpstan.org/)
* [Psalm](https://psalm.dev/), another popular static analyzer. You must use Composer though.
* [PHP Mess Detector](https://phpmd.org/), another analyzer.

## Coming next

Some CodinGame-specific tools and sites by the community
