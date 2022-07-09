# Finding bugs (_Xdebug_)

![Xdebug logo](../pic/xdebug-logo.png)

## Why?

If you start any puzzle in CodinGame, the starter code contains the line suggesting how to debug:

```php
// To debug (equivalent to var_dump): error_log(var_export($var, true));
```

Printing out messages or variable values to the error log is fine for a simple puzzle script. But sometimes we have a non-trivial bug and a bit more complex code or data structure, when such debug output becomes hard to manage.
As we have already installed `php` and `VS Code` locally, by adding `Xdebug` we will be able to:

* Set breakpoints (even conditional ones) in our code using the IDE.
* When we run our code locally, `php` will pause when reaching a breakpoint. The IDE will show the code line where we are.
* We can inspect the current values of variables and objects.
* We can continue running our code till another breakpoint is reached.
* ...or we can run our code step-by-step, one source code at a time.
* When we found the bug, just exit the debug mode, correct the source code and re-run to check if the problem is solved or not.

## Installing Xdebug

Again, I will not go into full details. The necessary steps are depending on OS, but [xdebug website](https://xdebug.org/docs/install) has detailed documentation. The following steps are valid only for my `XAMPP for Windows` installation:

* Download the latest Windows, 64-bit binary from the [download page](https://xdebug.org/download)
    * match with the major version of your local php installation, e.g. `8.1`
    * for XAMPP, use the thread-safe (TS) build
* Rename this file to `php_xdebug.dll` (so that we don't need to change the reference in the config file for every minor version update).
* Move the file to the `/ext` subdirectory of your local php installation (in my case to `c:\xampp\php\ext\`).
* Add these settings to your `php.ini` file:

```ini
[xdebug]
xdebug.mode=debug
xdebug.start_with_request = yes
xdebug.client_port = 9003
xdebug.client_host = "127.0.0.1"
xdebug.log = "C:\xampp\tmp\xdebug.log" ; or your tmp directory
xdebug.idekey = VSCODE
```

In a previous chapter I recommended to enable JIT. Unfortunately JIT and Xdebug does not work well together, so I recommend using 2 separate `php.ini` files:

The default `php.ini` is used when I don't want to debug, just to run the code (as fast as possible):

```ini
zend_extension=opcache
; zend_extension=xdebug
```

On the contrary, `php-debug.ini` has opcache commented out and xdebug enabled. Otherwise the 2 ini files are identical.

```ini
; zend_extension=opcache
zend_extension=xdebug
```

I also have a `phpd.bat` batch file in a directory in my `PATH`, together with the ini file:

```bat
@echo off
setlocal DISABLEDELAYEDEXPANSION
php -c %~dp0/php-debug.ini %*
```

So when I need to debug, I can just type `phpd` instead of `php`.

## Debugging in VS Code

* Add the `PHP Debug` extension to `VS Code` (_see the previous chapter of this playground_)
* Choose `Run` / `Add configuration...` menu, select "Listen for Xdebug"
    * The port should match what you set for `xdebug.client_port` in the `php.ini` file, e.g. `9003`
* Set a breakpoint by clicking on the left side of a source line.
    * A red circle should appear.
    * _Beware:_ if this line is in the middle of the loop, your run will pause in every iteration. You can set up a conditional breakpoint, eg. pause only if the loop variable equals a sepcific value.
* Select `Run` / `Start debugging` menu, choose the "Listen for Xdebug" config created earlier.
    * The status line at the bottom of the VS Code window changes its color, showing it is now listening to a debug event from Xdebug.
* Open a terminal windows (either within VS Code or separately).
* Run your code from the command line:

```bash
phpd my_solution.php < input_01.txt > output_01.txt 2>&1
```

* Note: `php` is replaced with `phpd` the batch file we created above, so it will use the php config file with xdebug enabled.
* If everything is OK, the execution should pause at the breakpoint, and VS Code shows the line with the breakpoint highlighted.
* Now you can use all the debug facilities: step over, inspect variable, etc. Find a VS Code tutorial if needed. This part is not specific to php.

## Useful links

* [Xdebug](https://xdebug.org/)

## Coming next

Checking and fixing the coding style (_PHP CodeSniffer, Php CS Fixer_)
