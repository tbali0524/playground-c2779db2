# Finding bugs (_Xdebug_)

![Xdebug logo](../pic/xdebug-logo.png)

## Why?

If you start any puzzle in CodinGame, the starter code contains the following line, suggesting how to debug:

```php
// To debug (equivalent to var_dump): error_log(var_export($var, true));
```

Printing out messages or variable values to the error log is fine for a simple puzzle script.
But sometimes we have a non-trivial bug and a bit more complex code or data structure, when such debug output becomes hard to manage.

As we have already installed `php` and `VS Code` locally, by adding `Xdebug` we will be able to:

* Set __breakpoints__ (even conditional ones) in our code using the IDE.
* When we run our code locally, `php` will pause when reaching a breakpoint. The IDE will show the source code line where the execution paused.
* We can __inspect__ the current values of variables and objects.
* We can continue running our code till another breakpoint is reached.
* ...or we can run our code __step-by-step__, one source code at a time (either jumping into function calls, or stepping over them).
* When we found the bug, just exit the debug mode, correct the source code and re-run to check if the problem is solved or not.

## Installing Xdebug

Again, I will not go into full details.
The necessary steps are depending on OS, but the [Xdebug website](https://xdebug.org/docs/install) has detailed documentation.
The following steps are valid for a `Windows` installation:

* Download the latest Windows, 64-bit binary from the [download page](https://xdebug.org/download).
    * Match with the major version of your local php installation, e.g. `8.1`.
    * For XAMPP, use the thread-safe (TS) build.
* Rename this file to `php_xdebug.dll`. (So that we won't need to change the reference in the config file after every minor version update.)
* Move the file to the `/ext/` subdirectory of your local php installation (in my case to `c:\xampp\php\ext\`).
* Add these settings to your `php.ini` file:

```ini
[xdebug]
xdebug.mode=develop,debug
xdebug.start_with_request = yes
xdebug.client_port = 9003
xdebug.client_host = "127.0.0.1"
xdebug.log = "C:\xampp\tmp\xdebug.log" ; or your tmp directory
xdebug.idekey = VSCODE
```

In a previous chapter I recommended to enable JIT.
Unfortunately, JIT and Xdebug does not work well together, so I suggest to use TWO separate `php.ini` files:

* The default `php.ini` is used when we don't want to debug, just to run the code (as fast as possible):

```ini
zend_extension=opcache
; zend_extension=xdebug
```

* On the other hand, `php-debug.ini` has `opcache` commented out, but `xdebug` enabled. Otherwise the two ini files are identical.

```ini
; zend_extension=opcache
zend_extension=xdebug
```

* I also have a `phpd.bat` batch file in a directory that is in my `PATH`, together with  the `php-debug.ini` file:

```bat
@echo off
setlocal DISABLEDELAYEDEXPANSION
php -c %~dp0/php-debug.ini %*
```

So when I need to debug, I can just type `phpd` instead of `php`.

_Beware: with `xdebug` enabled, your code runs 5x to 10x slower. So use debug mode ONLY when you are really debugging._

## Debugging in `VS Code`

* Add the `PHP Debug` extension to `VS Code`. _(See the previous chapter of this playground)_
* Choose `Run` / `Add configuration...` menu, select _"Listen for Xdebug"_
    * The port should match what you have set for `xdebug.client_port` in the `php.ini` file, e.g. `9003`
* Set a breakpoint by clicking on the left side of a source line.
    * A red circle should appear next to the line number.
    * _Beware:_ if this line is in the middle of the loop, your run will pause at EVERY iteration. If that was not what you wanted, you can set up a conditional breakpoint, e.g. pause only if the loop variable equals a specific value.
* Select `Run` / `Start debugging` menu, choose the _"Listen for Xdebug"_ config created earlier.
    * The status line at the bottom of the VS Code window changes its color, showing it is now listening to a debug event from Xdebug.
* Open a Terminal window (either within VS Code or separately).
* Run your code from the command line:

```bash
phpd my_solution.php < input_01.txt > output_01.txt 2>&1
```

* Note, that `php` is replaced with `phpd`, the batch file we created above, so it will use the php config file with XDebug enabled.
* If everything is OK, the execution should pause at the breakpoint, and VS Code shows the source code line with the breakpoint highlighted.
* Now you can use all the debug facilities: step over, inspect variable, etc. Find a VS Code tutorial if needed. This part of VS Code usage is not specific to php.

_Note:_ besides the 'Step Debugging' described above, Xdebug has additional features, such as Code Coverage Analysis, Garbage Collection Analysis, Profiling, Function Trace.
These can be turned on with the `xdebug.mode` setting in the `ini` file, but are beyond our scope, so check the [Xdebug documentation](https://xdebug.org/docs/) for details on how to use them.

## Useful links

* [Xdebug](https://xdebug.org/)

## Coming next

_"De gustibus non est disputandum."_ (There is no accounting for taste.) But did the Romans think about coding styles?
