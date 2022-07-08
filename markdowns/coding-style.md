# Checking and fixing the coding style (_PHP CodeSniffer, Php CS Fixer_)

## Why?

TODO

## Coding styles

TODO

## Installing PHP Dev Tools as

TODO

## Using phpcs

TODO

## Custom configfile for phpcs

Instead of passing command line arguments each time we run `phpcs`, we can create a config file and save it as `phpcs.xml` in tha main directory of our repo. As an example, here is my phpcs config. See [phpcs documentation] for more details.

```xml
<?xml version="1.0"?>
<ruleset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" name="codingame-solutions">
    <description>PHP_CodeSniffer configuration</description>
    <config name="php_version" value="70300"/>
    <arg name="cache" value=".tools/.phpcs.cache"/>
    <arg name="extensions" value="php"/>
    <arg name="encoding" value="utf-8"/>
    <arg name="tab-width" value="4"/>
    <arg name="report" value="summary"/>
    <arg name="report" value="full"/>
    <arg name="report-width" value="120"/>
    <arg name="colors"/>
    <arg value="sp"/>
    <!-- rules: https://github.com/squizlabs/PHP_CodeSniffer/blob/master/src/Standards/PSR12/ruleset.xml -->
    <rule ref="PSR12"/>
    <file>.</file>
    <exclude-pattern>*/.git/*</exclude-pattern>
    <exclude-pattern>*/.temp/*</exclude-pattern>
    <exclude-pattern>*/.tools/*</exclude-pattern>
    <exclude-pattern>*/.vscode/*</exclude-pattern>
    <exclude-pattern>*/vendor/*</exclude-pattern>
    <exclude-pattern>*/clash/*</exclude-pattern>
    <exclude-pattern>*/codegolf/*</exclude-pattern>
    <exclude-pattern>*/contest/*</exclude-pattern>
</ruleset>
```

## Using php-cs-fixer

TODO

## Custom configfile for php-cs-fixer

Similarly to `phpcs` you can also save your configuration for `php-cs-fixer`. In this case this is not an XML file, but a short php script which shall be be named `.php-cs-fixer.dist.php` or `.php-cs-fixer.php`.
Again, here is possible example, check out the docs for more details.

```php
<?php

declare(strict_types=1);

$finder = PhpCsFixer\Finder::create()
    ->in(__DIR__)
    ->name('*.php')
    ->ignoreVCSIgnored(true)
    ->exclude('.git/')
    ->exclude('.temp/')
    ->exclude('.tools/')
    ->exclude('.vscode/')
    ->exclude('clash/')
    ->exclude('codegolf/')
    ->exclude('contest/')
    ->notPath('optim/community/optim_com_cgfunge_prime.php')    // not a real php code
;
return (new PhpCsFixer\Config())
    ->setRules([
        '@PSR12' => true,
        '@PHP73Migration' => true,
    ])
    ->setRiskyAllowed(false)
    ->setCacheFile(__DIR__ . '/.tools/.php-cs-fixer.cache')
    ->setIndent("    ")
    ->setLineEnding("\n")
    ->setFinder($finder)
;
```

## Useful links

* [PSR-12 Coding Style](https://www.php-fig.org/psr/psr-12/)
* [PER Coding Style](https://www.php-fig.org/per/coding-style/)
* [PHP CodeSniffer](https://github.com/squizlabs/PHP_CodeSniffer)
* [PHP Coding Standards Fixer](https://cs.symfony.com/)
    * [configurator](https://mlocati.github.io/php-cs-fixer-configurator/), a summary page for all the available rules

## Coming next

Static code analysis to catch bugs before you run the code (_PhpStan_)
