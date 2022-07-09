# Using a dependency manager (_Composer_)

There are many more dev tools beyond the scope of this short intro. Some of them are not very useful in the context of CodinGame puzzles, but very important in real projects. Here I just briefly mention them without details.

![Composer logo](../pic/composer-logo.png)

## Why?

TODO

## Installing and using

TODO

## Basic config file

A very simple `composer.json` file:

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

## Creating custom scripts

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

## Useful links

* [Composer](https://getcomposer.org/)
* [Packagist](https://packagist.org/)
* [Phive](https://phar.io/)

## Coming next

Unit testing (_PHPUnit_)
