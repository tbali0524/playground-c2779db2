# Static code analysis to catch bugs before you run the code (_PHPStan_)

![PHPStan logo](../pic/phpstan-logo.png)

## Why?

In its original philosophy, PHP is quite forgiving: you can easily write messy code and get away with it without any error messages, sometimes even without warning messages. The typesystem is dynamic, and variable types are often silently converted as needed. While many people like this flexibility, it can bring weird bugs, which are sometimes very hard to find.

Moreover, as PHP is an interpreted language, there is no separate compile step. So, if there is an error in your code, it comes up only after you run it. This doesn't make too much difference at a solo CG puzzles, but can be problematic in applications that were meant for productive use.

A _static code analyser tool_ scans the codebase for possible errors WITHOUT running the code. It can detect much more than syntax errors: type juggling, invalid assumptions on function return types, and lots of other code smells. It does NOT replaces proper testing, but complements it.

There are several great code analyser tools available, in this playground we will use `PHPStan`. (_See links below for some others._)

## Installing `PHPStan`

You can install `PHPStan` following its [documentation](https://phpstan.org/user-guide/getting-started). While we can use some extensions only with `Composer`, but we won't really need them, so the simplest approach is the same as we did for other tools in the last chapter: download the `phar` file, move it to our `devtools` directory, and create a `phpstan.bat` shortcut:

```bash
curl -OL https://github.com/phpstan/phpstan/releases/latest/download/phpstan.phar
```

Now we can run a code analysis for the current directory with:

```bash
analyse --level max --verbose .
```

When running the first time against some old code, most likely there will be TONS of error messages.
The best approach is:

* Start with a low rule level, i.e. with `--level 0` only the most basic rules are checked.
* Go over the error messages one by one. Either modify your code to conform or add some exceptions
* When your code passes at this level, increase the level setting, rinse and repeat.
* The maximum level is 9, with some quite strict rules, but it is OK to stop the process earlier.
* You can also create a baseline. In this way, the old errors are ignored, but you can still check any new code you write for possible problems, even at a high rule level.

## Improving your code

While static analysis can help any code, for maximum benefit you shall start to use php in a much more 'typed' way.

* Use `declare(strict_types=1);` and adhere to it.
* Use type hints for all function parameters and function return types.
* Use property types in classes. This is supported only from `v7.4`, so for CG puzzle code that must run with php `v7.3`, you can use `@var` [PHPDocs](https://phpstan.org/writing-php-code/phpdocs-basics) (also known as 'DocBlocks') to declare the type of the properties. While the information in such PHPDocs is not enforced by the interpereter (it treats them as comments), phpstan uses it and checks if only that type is ever assigned to the given property.
* Many library functions can return 'false' as an error condition (e.g. `str_pos()`, but even `stream_get_line()`). You must always check the return value before assigning it to a property that was not meant to hold a boolean value.
    * That means that even an innocent `$a = explode(' ', stream_get_line(STDIN, 256, "\n"));` input parsing line in a CG puzzle is not clean code. While we _know_ that stream_get_line() should never fail in a CG puzzle, the analyser will not and warns you that explode expects a `string` as its second parameter and not a `string|bool`. The easiest way to deal with this is to add an `?: '0'` after the `stream_get_line()` call, so phpstan would know that even a false value would converted to string and no harm can happen.
* The php `array` is a very flexible type, but using the `array` word in type hints does not tell much about its actual contents. At high rule levels you need to add an additional `PHPDocs` block to define the array's 'shape'. For example, `int[]` is a vector of integers, while `array<string, float>` is an associative array, where the keys are strings and the values are floats. At first doing this feels tedious, but the additional checking capability can prevent many bugs. (And you start feeling like you are coding in Java... :-) )
* In many CG puzzle codes, I made debug logging toggleable by using a global `const DEBUG = true;` line, then later using `if (DEBUG) { error_log('...'); }` conditionals. At some rule level, `phpstan` will figure out that the condition is always true (or false) and will complain. You can silence such 'false positives' by adding a `// @phpstan-ignore-next-line` line to your source, or add the constant to a `dynamicConstantNames` entry in your config file _(see below)_.

## Custom configuration file for `PHPStan`

Instead of passing command line arguments each time we run `phpstan`, we can create a config file and save it as `phpstan.neon` in the main project directory. The NEON format is essentially a YAML file with some additional features. See the [phpstan config documentation](https://phpstan.org/config-reference) for more details.

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

The following documents are _must reads_ for any PHP developer:

* [Clean Code PHP](https://github.com/jupeter/clean-code-php), a collection of principles on "producing readable, reusable, and refactorable software in PHP".
* [PHP The Right Way](https://phptherightway.com/), a very thorough quick reference for PHP best practices.

## Coming next

Some CodinGame-specific tools and sites by the community
