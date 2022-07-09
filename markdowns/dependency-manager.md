# Using a dependency manager (_Composer_)

There are many more dev tools beyond the scope of this short intro playground. Some of them are not very useful in the context of CodinGame puzzles, but very important in real projects. Here I just briefly mention them without details.

![Composer logo](../pic/composer-logo.png)

## Why?

TODO

## Installing and basic usage

A `composer` tutorial is beyond the scope of this playground, so I will be very brief.

* Download and install it from its [website](https://getcomposer.org/), following the instructions.
* To use it in a project, you need a `composer.json` file. This file will contain the description of all your project dependencies (including allowed version numbers), and some other settings.
    * You can create it manually with any text editor (including `VS Code` of course);
    * ... or you can run `composer init`, which will ask some questions on the console, and then creates the file for you.
* The `composer install` command will download and install all your stated dependencies in the `vendor` subdirectory.
    * This will also create a `composer.lock` file that locks the dependencies of your project to a known state.
* You can add third-party packages as dependencies with `composer require vendor/packagename`.
    * Add `--dev` argument to specify that the package is required only in a developement environment, but not in production (e.g. a dev tool).
    * [Packagist](https://packagist.org/) is the main Composer repository for all the packages available.
* Later you can upgrade all the packages you are using in your project with `composer update`.

In case of Codingame puzzles, we cannot use any external packages. But we can still specify what php version and what php libraries our code needs. So a possible, very simple `composer.json` file:

```json
{
    "name": "your-github-profile-name/reponame",
    "description": "codingame puzzle solutions",
    "type": "project",
    "require": {
        "php": "^7.3|^8.0",
        "ext-bcmath": "*",
        "ext-ctype": "*",
        "ext-mbstring": "*"
    }
}
```

You can check if all your stated requirements are met on your local machine with:

```bash
composer check-platform-reqs
```

## Creating custom scripts

A convenience feature of composer is the ability to create custom commands or scripts. In the previous chapters we have used several tools from the command line but needed to start them with several arguments (or create additional batch files). We can use composer scripts as an alternative.

For example, the following fields can be added to the basic `composer.json` file above:

```json
    "scripts": {
        "cs" :      "phpcs",
        "cs-fixer": "php-cs-fixer fix --dry-run --show-progress=dots --ansi --diff -vv",
        "stan":     "phpstan --ansi --verbose",
        "qa" : [
            "@cs",
            "@cs-fixer",
            "@stan"
        ],
        "clean" : [
            "if exist .tools\\.phpcs.cache          del .tools\\.phpcs.cache",
            "if exist .tools\\.php-cs-fixer.cache   del .tools\\.php-cs-fixer.cache",
            "if exist .tools\\phpstan\\             rmdir /S /Q .tools\\phpstan"
        ]
    },
    "scripts-descriptions": {
        "cs":       "Check coding style compliance to PSR12 with phpcs",
        "cs-fixer": "Check coding style compliance to PSR12 plus extra rules with php-cs-fixer (no fix applied)",
        "stan":     "Run static analysis with phpstan",
        "qa":       "Run code quality checks: phpcs, php-cs-fixer and phpstan",
        "clean":    "Delete dev tools cache (Windows only)"
    }
```

Here we defined several shortcuts. Now we can simply run the `PHP CS Fixer` tool with some predefined arguments with

```bash
composer cs-fixer
```

...or similarly, PHPStan with `composer stan`.

Multiple tools are run by the custom `composer qa` script, which I use as some 'catch-all' command before I commit at the end of a coding session. It could be further automated with some CI/CD tools such as GitHub Actions, but it is more than enough for me for the CG puzzles.

(Note the above scripts still assume that the tools' config files are in the main project directory, as we discussed it in earlier chapters.)

The `scripts-descriptions` part is fully optional, these descriptions are used only by the `composer list` and `composer help` commands.

## Useful links

* [Composer](https://getcomposer.org/)
* [Packagist](https://packagist.org/)

## Coming next

Unit testing (_PHPUnit_)
