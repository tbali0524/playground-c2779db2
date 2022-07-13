# Static code analysis to catch bugs before you run the code (_PHPStan_)

![PHPStan logo](../pic/phpstan-logo.png)

## Why?

In its original philosophy, PHP is quite forgiving: you can easily write messy code and get away with it without any error messages, sometimes even without warnings.
The typesystem is dynamic, and variable types are often silently converted by the interpreter as needed.
While many people like this flexibility, it can bring weird bugs, which are sometimes very hard to find.

Moreover, as PHP is an interpreted language, there is no separate compile step.
So, if there is an error in your code, it comes up only after you run it.
This doesn't make too much difference at a solo CG puzzle, but can be problematic in real-life applications that were meant for productive use.

A _static code analyser tool_ scans the codebase for possible errors WITHOUT running the code.
It can detect much more than just syntax errors: type juggling, invalid assumptions on function return types, and lots of other code smells.
It does NOT replace proper testing, but complements it.

There are several great code analyser tools available, in this playground we will use `PHPStan`.
_(See the links section at the bottom for some others.)_

## Installing `PHPStan`

You can install `PHPStan` following its [documentation](https://phpstan.org/user-guide/getting-started).
While we can use some PHPStan extensions only with `Composer`, but we won't really need them, so the simplest approach is the same as we did for the other tools in the previous chapter: download the `Phar` file, move it to our `devtools` directory, and create a `phpstan.bat` helper:

```bash
curl -OL https://github.com/phpstan/phpstan/releases/latest/download/phpstan.phar
```

```bat
@echo off
setlocal DISABLEDELAYEDEXPANSION
set BIN_TARGET=%~dp0/phpstan.phar
php "%BIN_TARGET%" %*
```

Now we can run a code analysis for the current directory with:

```bash
phpstan analyse --level max --verbose .
```

When running the first time against some old code, most likely there will be TONS of error messages.

The best approach is this:

* Start with the lowest rule level by using the command line argument `--level 0`. Now only the most basic rules are checked.
* Go over the error messages one by one. Either modify your code to conform, or add some exceptions _(see later)_.
* When your code passes at this rule level, increase the level setting, rinse and repeat.
* The maximum level is 9, with some quite strict rules, but it is absolutely OK to stop the process earlier.
* You can also create a _baseline_. In this way, the old errors are ignored, but you can still check any new code you write for possible problems, even at a high rule level.

## Improving your code

While static analysis can help any code, for maximum benefit you shall start to use php in a much more 'typed' way.

* Use `declare(strict_types=1);` and adhere to it.
* Use type hints for all function parameters and function return types.
* Use property types in classes. This is supported only from `v7.4`, so for a CG puzzle code that must run with php `v7.3`, you can use `/** @var ... */` [PHPDocs](https://phpstan.org/writing-php-code/phpdocs-basics) (also known as _DocBlocks_) to declare the type of the property. While the information in such PHPDocs is not enforced by the interpreter at runtime (treated as a comment), `phpstan` uses it during its analysis, and checks whether a value of an other type is ever assigned to that property or not.
* Many library functions can return _false_ as an error condition (e.g. `str_pos()`, but even `stream_get_line()`). You must always check the return value before assigning it to a property, or pass it to a function that was not meant to receive a boolean value.
    * That means that even an innocent `$a = explode(' ', stream_get_line(STDIN, 256, "\n"));` input parsing line in a CG puzzle is not a "clean code". While we _know_ that `stream_get_line()` would never fail in a CG puzzle, the analyser will _not_, and warns you that `explode()` expects a _string_ as its second parameter and not a _string|bool_. The easiest way to deal with this is to add an `?: '0'` after the `stream_get_line()` call, so phpstan will now deduct that a _false_ return value would be converted to _string_, and no harm can happen.
* The php `array` is a very flexible type, but using the `array` word in type hints does not tell much about its actual contents. At high rule levels, you need to add an additional `PHPDocs` block to define the array's real _shape_. For example, `int[]` is a vector of integers, while `array<string, float>` is an associative array where the keys are strings and the values are floats. More complex shapes are also supported. At first, doing this feels tedious, but the additional analysing capability can prevent many bugs. _(And you might start feeling like you are coding in Java... :-) )_
* In many CG puzzle codes, I made debug logging toggleable by using a global `const DEBUG = true;` line, then later using `if (DEBUG) { error_log('...'); }` conditionals. At some rule level, `phpstan` will figure out that the condition is always _true_ (or _false_) and will complain. You can silence such 'false positives' by adding a `// @phpstan-ignore-next-line` line to your source. Another possibility is to add the constant to a `dynamicConstantNames` entry in your config file _(see below)_.

## Custom configuration file for `PHPStan`

Instead of passing command line arguments each time we run `phpstan`, we can create a config file and save it as `phpstan.neon` in the main project directory.
The _NEON file format_ is essentially a YAML file with some additional features.
See the [PHPStan Config Documentation](https://phpstan.org/config-reference) for more details.

As an example, here is a possible phpstan config file:

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

Having this file in your project directory, you can simply run `phpstan --verbose`

## Useful links

* [PHPStan](https://phpstan.org/)
* [Psalm](https://psalm.dev/), another popular static code analyser. You must use `Composer` though.
* [PHP Mess Detector](https://phpmd.org/), another code analyser.

The following documents are __must reads__ for any PHP developer:

* [Clean Code PHP](https://github.com/jupeter/clean-code-php), a collection of principles on "producing readable, reusable, and refactorable software in PHP".
* [PHP The Right Way](https://phptherightway.com/), a very thorough quick reference for PHP best practices.

## Coming next

Some CodinGame-specific tools and sites by the community.
