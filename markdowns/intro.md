# PHP Dev Tools (for CodinGame or anywhere else)

Note: This playground is work in progress!

## Intro and motivation

[CodinGame](https://www.codingame.com/) is a great coding site where you can practice and improve your coding skills in a fun way by solving puzzles or writing code for a bot that will compete with other coders' bots in an arena (by playing a game with a specific set of rules).

At `CG`, you don't really have to bother about setting up any local development environment: just start to write your code directly on the CG website using your browser, run the provided test cases there (for solo puzzles), and submit your solution when you feel ready.

Despite this, it brings some advantages if you make the effort and spend the time to set up a local dev environment with some additional tooling - especially if you already have lots of puzzle solutions and some longer code as well. The experience will also come in handy in other projects, outside the context of `CodinGame`. Most of the discussion is also applicable elsewhere.

This `Tech.io` playground explores several tools to make your life easier as a developer. Topics we will cover:

* Running your code locally (_php_)
* Using a code repository (_git, GitHub_)
* Using an IDE (_Visual Studio Code_)
* Finding bugs (_Xdebug_)
* Checking and fixing the coding style (_PHP CodeSniffer, Php CS Fixer_)
* Static code analysis to catch bugs BEFORE you run the code (_PhpStan_)
* Extra: Some great CodinGame-specific tools and websites by the community

We will also very briefly touch some tools that are less relevant for CodinGame puzzles, but it is good to know them for other projects:

* Using a dependency manager (_Composer_)
* Unit testing (_PHPUnit_)
* Generating documentation (_phpDocumentor_)

My goal with this article is NOT to provide a full tutorial for each tool, I will really just highlight the basics (with an extra emphasis on __why__ to use them), and show how I personally use them with my CG activities. I try to be beginner-friendly (professional php developers are surely using these or similar other tools already), but to limit article length, I cannot cover all the details of an installation and usage. Links are provided for further study.

I will not discuss any puzzle, algorithm, or the `PHP` language itself here, the topic is solely the dev environment and tooling. While the specific discussion is for PHP, most topics (especially the _Why?_ parts) are valid for other languages (although with a different toolset).

## Contributions welcome

This playground is far from complete. If you have suggestions for improvements, please:

* send me ([TBali](https://www.codingame.com/profile/08e6e13d9f7cad047d86ec4d10c777500155033)) a __private message__ on CodinGame;
* or give a __pull request__ directly to the [github repo](https://github.com/tbali0524/playground-c2779db2) of this playground;
* or just leave a comment here on `Tech.io`.

## Let's get started!
