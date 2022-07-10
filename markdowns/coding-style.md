# Checking and fixing the coding style (_PHP CodeSniffer, PHP CS Fixer_)

## Why?

The PHP language does not care too much about whitespaces.
You can indent your code as you like, or throw everything in a single line.
You can put your braces `{ }` in separate lines or not.
You can even make your code look like an ascii art.
You can write keywords in lowercase, uppercase or mixedcase.
(Ever tried an `eChO`?)
There are some language construct with multiple versions (e.g. `array()` or `[]`).
No matter: your code will still work exactly the same way.

Yet, __coding style still matters__.
Actually, a source code is written not only for the php interpreter, but also for humans.
In case of CodinGame puzzles, it is just __YOU__, who might want to be able to read and quickly understand your own code years later.
For real life projects they are also team members, or in case of open source projects it can be a much broader audience.

Adopting an easily readable, consistent coding style decreases the cognitive load when reading the code, thus saves time and reduces the chance of misunderstanding and bugs.

## Coding style standards

There are several coding style guides available for php:

* [PSR-1](https://www.php-fig.org/psr/psr-1/): Basic Coding Standard, with just a limited number of rules.
* [PSR-2](https://www.php-fig.org/psr/psr-2/): Coding Style Guide, a more detailed version. Now _deprecated_ and replaced by `PSR-12`.
* [PSR-12](https://www.php-fig.org/psr/psr-12/): Extended Coding Style. Nowadays acting as a common ground, on which other, more opinionated styling standards are based.
* [PER Coding Style](https://www.php-fig.org/per/coding-style/): Many new features appeared in the PHP language since `PSR-12` was finalized in 2019. `PER` stands for "PHP Evolving Recommendation". As the name implies, this coding standard gets regular updates, at least after every minor php release. It can be considered as a _modernized `PSR-12`_.
* _Project-specific coding styles:_ Some major open source php projects also introduced their own coding standards ([Symfony](https://symfony.com/doc/current/contributing/code/standards.html), etc). They might extend `PSR-12` with additional rules, or even override some of the original rules there.

_Note: PSR stands for "PHP Standards Recommendations". PSRs are de-facto standards maintained by [PHP-FIG](https://www.php-fig.org/) (Framework Interoperability Group). Besides the coding guides above, there are several standardized interfaces for some common use-cases, a specification for autoloading classes, etc._

It is good if you are somewhat familiar with your chosen coding style, but you don't have to know all its rules.
There are tools that can check (and even automatically fix) your code.
In this playground we will check out the two most popular tools for dealing with PHP coding standards: `PHP CodeSniffer` and `PHP CS Fixer`.

It doesn't care too much exactly which ruleset you use, just be consistent.
`PSR-12` is a good bet in most cases.
Both tools discussed allow you to tweak the rules and compose your own coding style ruleset.
However, it is usually not worth the time.
The above guides are already there, so you don't have to spend too much time on styling decisions.

## Alternative ways of installing a PHP dev tool

Before we go into details about the tools to check and fix the coding style, let's see how can we install a php dev tool _in general_.
__This will be valid also for the other tools to be introduced in later chapters.__

* If using a dependency manager (`Composer`), you can install the tool as a dev-environment-only dependency into your project. It will appear in the `/vendor` subdirectory. (We added this directory name to `.gitignore` exactly to prevent any third-party packages to go into your repository.) However, this approach is not ideal:
    * You will have multiple copies of the same tools, one in each of your projects.
    * These tools have also their own package dependencies, which might collide with your own dependencies. (This is not a concern with CodinGame puzzles, where you CANNOT use any third party packages.)
* Another option is to install the tool with `Composer` as a _global_ package.
    * If doing this, then at least you have only a single copy per tool.
    * Different tools might still depending on different versions of the same package.
* Download the tool as a single __PHAR__ file. Phar means 'php archive', it contains everything needed to run the application (required packages, additional files, etc). You need to pass the phar file to the php interpreter just as you would do with a single php script.
* Use [PHIVE](https://phar.io/), the _PHAR Installation and Verification Environment_ to manage your dev tools. It makes downloading and updating the Phar files easier.

Most dev tools' own documentation recommend NOT to install them with `composer`, but to use the PHAR version (either downloading manually, or with `PHIVE`).

## Installing PHP CodeSniffer

After so much theory, let's finally install [PHP CodeSniffer](https://github.com/squizlabs/PHP_CodeSniffer) with any of the methods described on its GitHub site.

CodeSniffer is actually a set of TWO command-line tools:

* `phpcs` checks your code for any violations to a coding standard.
* `phpcbf` also modifies your code to correct these violations (at least those that can be automatically fixed).

For all the php dev tools I use, I did the following, but feel free to use a different approach:

* Create a `c:\devtools` directory and add it to the `PATH`.
* Download the Phar file and move it to the above directory.
    * _Note: Latest Windows 10/11 already includes curl. If yours not, then any download method should work, even a browser._

```bash
curl -OL https://squizlabs.github.io/PHP_CodeSniffer/phpcs.phar
```

* For convenience, create a shortcut batch file `phpcs.bat`:

```bat
@echo off
setlocal DISABLEDELAYEDEXPANSION
set BIN_TARGET=%~dp0/phpcs.phar
php "%BIN_TARGET%" %*
```

* Do similar steps for the fixer component, `phpcbf`.

## Using CodeSniffer

If you have some php source files in your project directory, you can check if they conform to the `PSR-12` standard:

```bash
phpcs --standard=PSR12 --report=full --report-width=120 --colors -p -s .
```

You will get a list of all the rule violations per file, with line numbers and detailed explanations. Adding `--report=code` even shows the problematic source lines and their surroundings.

You have multiple options to deal with the error messages:

* Review and correct them manually in `VS Code`. This is rather tedious, I would recommend to do it only a few times in the beginning, just to better understand the `PSR-12` coding style requirements. (Soon, you will find that writing conforming code became natural for you, and you will get much less errors from `phpcs` with your newer code.

* Let CodeSniffer correct them for you automatically. Don't worry, the fixer does not make any modification that would change the behaviour of your code. Only the styling will be changed.

```bash
phpcbf --standard=PSR12 -p .
```

* If you cannot (or don't want to) correct a specific rule violation, you can let `phpcs` know that you want to ignore this error inn the future.
    * A typical example: with _CodinGame_ your code must be in a single file, so whenever you use functions or classes, you instantly violate the following `PSR-1` rule: _"A file should declare new symbols (classes, functions, constants, etc.) and cause no other side effects, but should not do both."_
    * If having multiple classes, another rule is triggered: _"Each class must be in a file by itself"_.
    * Adding the following line to the top of your source file (after the opening `<?php` tag) makes these errors go away:
        * _(Note: The name of the rule to disable was shown in the error message.)_

```php
// phpcs:disable PSR1.Files.SideEffects, PSR1.Classes.ClassDeclaration.MultipleClasses
```

* You can also ignore individual source lines from being checked by adding a `// phpcs:ignore` line before (or at the end of) the given line.

## Custom configuration file for `phpcs`

Instead of passing command line arguments each time we run `phpcs`, we can create a config file and save it as `phpcs.xml` in the main project directory.
See the [phpcs documentation](https://github.com/squizlabs/PHP_CodeSniffer/wiki) for more details.

As an example, here is a possible phpcs config file:

```xml
<?xml version="1.0"?>
<ruleset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" name="codingame-solutions">
    <description>PHP_CodeSniffer configuration</description>
    <config name="php_version" value="70300"/>
    <arg name="cache" value=".tools/.phpcs.cache"/>
    <arg name="extensions" value="php"/>
    <arg name="encoding" value="utf-8"/>
    <arg name="tab-width" value="4"/>
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

Having this file in your project directory, you can simply run `phpcs` without any command line arguments.

## Using PHP CS Fixer

![PHP CS Fixer logo](../pic/php-cs-fixer-logo.png)

[PHP Coding Standards Fixer](https://cs.symfony.com/) is another popular solution to check and fix the coding style. It has similar features to PHP CodeSniffer, so I will be brief.

* Again, as discussed above, there are multiple ways to install the tool. I download the `Phar` file, move it to my `c:\devtools` directory, and create a `php-cs-fixer.bat` helper:

```bash
curl -L https://cs.symfony.com/download/php-cs-fixer-v3.phar -o php-cs-fixer.phar
```

```bat
@echo off
setlocal DISABLEDELAYEDEXPANSION
set BIN_TARGET=%~dp0/php-cs-fixer.phar
php "%BIN_TARGET%" %*
```

* Check the style without modifying anything with the `--dry-run` option:

```bash
php-cs-fixer fix --dry-run --rules="@PSR12,@PHP73Migration" --show-progress=dots --ansi --diff -vv .
```

* To fix the violations automatically, the same tool is used, but now without adding `--dry-run`:

```bash
php-cs-fixer fix --rules="@PSR12,@PHP73Migration" --show-progress=dots --ansi --diff -vv .
```

## Custom configuration file for `php-cs-fixer`

Similarly to `phpcs`, you can also save your configuration for `php-cs-fixer`.
In this case, this is not an _XML_ file, but a short _php script_, which shall be named `.php-cs-fixer.dist.php` or `.php-cs-fixer.php`.

Here is a possible example, check out the [documentation](https://github.com/FriendsOfPHP/PHP-CS-Fixer/tree/master/doc) for more details.

* It includes some __'risky'__ rules, such as "always add a `declare(strict_types=1);` line if it was missing". Such change is not just coding style but it alters behaviour and __CAN break your code__, so these should be used very cautiously.

* On top of `PSR-12` it includes the much more opinionated `Symfony` coding standard, however I overrode some rules manually.

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
    // available rulesets and rules: https://github.com/FriendsOfPHP/PHP-CS-Fixer/tree/master/doc
    ->setRules([
        '@PSR12' => true,
        '@PHP81Migration' => true,
        '@PHP80Migration:risky' => true,    // this also needs: ->setRiskyAllowed(true)
        '@Symfony' => true,

        // override some @PHPxxMigration rules, because CG supports only php v7.3
        'assign_null_coalescing_to_coalesce_equal' => false, // override @PHP81Migration, ??= requires php v7.4
        'use_arrow_functions' => false,     // override @PHP80Migration:risky, => fn() requires php v7.4
        'modernize_strpos' => false,        // override @PHP80Migration:risky, str_contains() requires php v8.0

        // override some @Symfony rules
        'binary_operator_spaces' => ['operators' => ['=' => null, '=>' => null, 'and' => null, 'or' => null]],
        'blank_line_before_statement' => false,
        'class_attributes_separation' => false,
        'concat_space' => ['spacing' => 'one'],
        'increment_style'=> ['style' => 'post'],
        'phpdoc_to_comment' => false,
        'yoda_style' => false,
    ])
    ->setRiskyAllowed(true)
    ->setCacheFile(__DIR__ . '/.tools/.php-cs-fixer.cache')
    ->setIndent("    ")
    ->setLineEnding("\n")
    ->setFinder($finder)
;
```

This config file defines which folders and files to include/exclude, and which rules to apply.
We still need to apply some additional command line arguments:

```bat
php-cs-fixer fix --dry-run --show-progress=dots --ansi --diff -vv .
```

## Checking and fixing the coding style in `VS Code`

While in the examples above we used the tools from the command line, both `phpcs` and `php-cs-fixer` have good VS Code extensions. _(See the 'VS Code' chapter of this playground.)_
After enabling and configuring the extension, VS Code can check (and fix) the coding style within the IDE editor, immediately as you type in your code.

## Useful links

* [PSR-12 Coding Style](https://www.php-fig.org/psr/psr-12/)
* [PER Coding Style](https://www.php-fig.org/per/coding-style/)
* [PHP CodeSniffer](https://github.com/squizlabs/PHP_CodeSniffer)
* [PHP Coding Standards Fixer](https://cs.symfony.com/)
    * [php-cs-fixer configurator](https://mlocati.github.io/php-cs-fixer-configurator/), a nice summary page with all the available rules

## Coming next

Some clever tools can check much more than just the coding style.
