# Checking and fixing the coding style (_PHP CodeSniffer, Php CS Fixer_)

## Why?

The PHP language does not care too much about whitespaces. You can indent your code as you like, or throw everything into a single line. You can put your braces `{ }` in separate lines or not. You can write keywords in lowercase, uppercase or mixedcase. (Ever tried an `eChO`?) There are some language construct with multiple versions (e.g. `array()` or `[]`). Your code will work exactly the same way.

Coding style still matters. Actually, a source code is written not only for the php interpreter, but also for humans. In case of CodinGame puzzles it is just you, who might want to be able to read and quickly understand your own code years later. For real life projects it is also team members, or in case of open source projects a much wider audience.

Adopting an easily readable, consistent coding style decreases the cognitive load when reading code, thus saves time and reduces the chance of misunderstanding and bugs.

## Coding style standards

There are several coding style guides available for php:

* [PSR-1](https://www.php-fig.org/psr/psr-1/): Basic Coding Standard, with just a limited number of rules.
* [PSR-2](https://www.php-fig.org/psr/psr-2/): Coding Style Guide, a more detailed version. Now _depreciated_ and replaced by `PSR-12`.
* [PSR-12](https://www.php-fig.org/psr/psr-12/): Extended Coding Style.
* [PER Coding Style](https://www.php-fig.org/per/coding-style/): Many new features appeared in the PHP language since `PSR-12` was finalized in 2019. `PER` stands for "PHP Evolving Recommendation" and this coding standard gets regular updates, at least after every minor php release. It can be considered as a modernized `PSR-12`.
* Project-specific coding styles: Some major php projects also introduced their own coding standards ([symfony](https://symfony.com/doc/current/contributing/code/standards.html), etc). They might extend `PSR-12` with additional rules, or even override some of the rules there.

_Note: PSR stands for "PHP Standards Recommendations". This is a list of de-facto standards (besides the coding guides also some common interfaces, etc), maintained by [PHP-FIG](https://www.php-fig.org/) (Framework Interoperability Group)._

All the tools discussed later will allow you to tweak the rules and compose your own coding standard. However, it is usually not worth the time. The above rulesets are exactly there so that you don't have to spend too much time on coding styles.

Also, it doesn't care that much which one you use, just be consistent. `PSR-12` is a good bet.

## Alternative ways of installing a PHP dev tool

Before we go into details about the tools to check and fix the coding style, let's see how can we install a php dev tool in general. This will apply also for the tools to be introduced in a later chapter.

* If using a dependency manager (`Composer`), you can install the tool as a dev-time dependency into your project. It will appear in the `/vendor` subdirectory. (We added this directory name to `.gitignore` exactly to prevent the packages going into your repository.) However, this is not ideal:
    * You will have multiple copies of the same tools, one in each of your projects.
    * These tools have also their own package dependencies, which might collide with your dependencies. (This is not a concern with CodinGame puzzles, where you CANNOT use any thrid party packages.)
* Another option is to install them with `composer` as a _global_ package.
    * Now at least you have only single copy per tool.
    * Different tools might still require different versions of the same dependency package.
* Download the tool as a single __PHAR__ file. Phar means `php archive`, and it contains everything needed to run the program (packages, etc), you just pass the phar file to the php interpreter.
* Use [PHIVE](https://phar.io/), the _PHAR Installation and Verification Environment_ to manage your tools. It makes downloading and updating the phar files easier.

Most dev tools recommend not to install them with `composer`, but to use the PHAR version (either manually or with `Phive`).

## Installing PHP CodeSniffer

After so much theory, let's finally install [PHP CodeSniffer](https://github.com/squizlabs/PHP_CodeSniffer) with any of the methods described on its github site.

CodeSniffer is actually a set of TWO tools: `phpcs` checks your code for any violations to a coding standard, while `phpcbf` modifies your code to correct the violations that can be automatically fixed.

For all the php dev tools I did the following, but feel free to use a different approach:

* Create a `c:\devtools` directory and add it to the `PATH`.
* Download the phar file and move it to the above directory:
    * _Note: Windows 10/11 already includes curl._

```bash
curl -OL https://squizlabs.github.io/PHP_CodeSniffer/phpcs.phar
```

* Create a shortcut batch file `phpcs.bat`:

```bat
@echo off
setlocal DISABLEDELAYEDEXPANSION
SET BIN_TARGET=%~dp0/phpcs.phar
php "%BIN_TARGET%" %*
```

* Do similar steps for the fixer component, `phpcbf`.

## Using CodeSniffer

If you have some php source files in your project directory, you can check if they conform to the `PSR-12` standard:

```bash
phpcs --standard=PSR12 --report=full --report-width=120 --colors -p -s .
```

You will get a list of all the rule violations per file, with line numbers and detailed explanations. Adding `--report=full` even shows the problematic source line with surrounding lines.

You have multiple options to deal with the error messages:

* Review and correct them manually in VS Code. This is rather tedious, I woukd recommend it to do it only a few times in the beginning, just to better understand the `PSR-12` coding style requirements. (Soon, you will find that writing conforming code becomes natural and you will have much less errors with phpcs for your newer code.

* Let CodeSniffer correct them for you automatically. Don't worry, the fixer does not make any modification that would change the behaviour of your code, only the styling will be changed.

```bash
phpcbf --standard=PSR12 -p .
```

* If you cannot or you don't want to correct the violation, you can let `phpcs` know that you want to ignore this error.
    * A typical example with CodinGame, where all your code must be in a single file, is that when you use functions or classes, you instantly violate the following PSR-1 rule: "A file should declare new symbols (classes, functions, constants, etc.) and cause no other side effects, but should not do both.".
    * If having multiple classes, another rule is triggered: "Each class must be in a file by itself".
    * Adding the following line to the top of your source file (after the opening `<?php` tag) makes the errors go away. (The name of the rule to disable was shown in the error message.)

```php
// phpcs:disable PSR1.Files.SideEffects, PSR1.Classes.ClassDeclaration.MultipleClasses
```

You can also ignore individual source lines from being checked by adding a `// phpcs:ignore` line before the given line.

## Custom configfile for `phpcs`

Instead of passing command line arguments each time we run `phpcs`, we can create a config file and save it as `phpcs.xml` in the main project directory. See the [phpcs documentation](https://github.com/squizlabs/PHP_CodeSniffer/wiki) for more details.

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

## Using Php CS Fixer

[Php CS Fixer](https://cs.symfony.com/) is another popular solution to check and fix the coding style. It has similar features as PHP CodeSniffer, so I will be brief.

* Again, there are multiple ways to install it, as discussed above. I download the `phar` file, move it to my `devtools` directory, and create a `php-cs-fixer-bat` shortcut.

```bash
curl -L https://cs.symfony.com/download/php-cs-fixer-v3.phar -o php-cs-fixer.phar
```

* Check the style without modifying anything with the `--dry-run` option:

```bash
php-cs-fixer fix --dry-run --rules="@PSR12,@PHP73Migration" --show-progress=dots --ansi --diff -vv .
```

* To fix the violations automatically, the same tool is used, but without adding `--dry-run`:

```bash
php-cs-fixer fix --rules="@PSR12,@PHP73Migration" --show-progress=dots --ansi --diff -vv .
```

## Custom configfile for `php-cs-fixer`

Similarly to `phpcs`, you can also save your configuration for `php-cs-fixer`. In this case this is not an XML file, but a short php script that shall be named `.php-cs-fixer.dist.php` or `.php-cs-fixer.php`.

Again, here is a possible example, check out the docs for more details. The example below also includes some 'risky' rules, such as adding a `declare(strict_types=1);` line if it was missing. Such change alters behaviour so should be used catiously.

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
        '@PHP81Migration' => true,
        '@PHP80Migration:risky' => true,    // this also needs: ->setRiskyAllowed(true)
        'assign_null_coalescing_to_coalesce_equal' => false, // override @PHP81Migration, ??= requires php v7.4
        'use_arrow_functions' => false,     // override '@PHP80Migration:risky, => fn() requires php v7.4
        'modernize_strpos' => false,        // override '@PHP80Migration:risky, str_contains() requires php v8.0
    ])
    ->setRiskyAllowed(true)
    ->setCacheFile(__DIR__ . '/.tools/.php-cs-fixer.cache')
    ->setIndent("    ")
    ->setLineEnding("\n")
    ->setFinder($finder)
;
```

## Checking and fixing the coding style in `VS Code`

While in the examples above we used the tools from the command lines, both `phpcs` and `php-cs-fixer` have VS Code extension (_see the VS Code chapter of this playground_). After enabling and configuring these extension VS Code can check (and fix) the coding style within the editor.

## Useful links

* [PSR-12 Coding Style](https://www.php-fig.org/psr/psr-12/)
* [PER Coding Style](https://www.php-fig.org/per/coding-style/)
* [PHP CodeSniffer](https://github.com/squizlabs/PHP_CodeSniffer)
* [PHP Coding Standards Fixer](https://cs.symfony.com/)
    * [configurator](https://mlocati.github.io/php-cs-fixer-configurator/), a nice summary page with all the available rules

## Coming next

Static code analysis to catch bugs before you run the code (_PhpStan_)
