This file describes how to configure your machine to work on the Pallene compiler, and how to run the test suite.
We welcome any pull requests

### Configuring your text editor

We appreciate if you can configure your text editor to use the appropriate indentation.
The easiest way to do this is to install the [EditorConfig](https://editorconfig.org/) for your favorite text editor.
The plugin will read our `.editorconfig` file and automatically set the correct indentation and much more.

Our coding style uses 4 spaces for indentation, and a line width of 100 columns.
A common mistake that we see is when the line width is set to 80.

### Running the linter

Our continuous integration script runs some automated tests on every pull request that we receive.
These scripts run the Luacheck linter, and also test some other things, like if every file has a copyright header.

We encourage you to run these tests locally before submitting a pull request, by running the `./run-lint`.
You will need to have Luacheck installed on your machine.
You can do so using LuaRocks; for full instructions see our [`.luacheckrc`](.luacheckrc) file.

### Running the test suite

We use [Busted](http://olivinelabs.com/busted/) to run our test suite.
It can be installed using LuaRocks:

```sh
$ luarocks install busted
```

To run the test suite, run the `./run-tests` script in this project's root directory.
Tip: if GNU parallel is installed, it can speed things up by running multiple tests in parallel.

```sh
$ ./run-tests                       # Run all tests
$ ./run-tests spec/parser_spec.lua  # Run just one of the test suite files
```

The `./run-tests` script accepts the same command-line flags as `busted`.
If you are debugging an unhandled exception in a test case, the following ones might help:

Flag                  | Effect
--------------------- | --------------------------------------------------------
./run-tests -v        | Verbose output, including the stack trace
./run-tests -k        | Run all tests even if some tests are failing
./run-tests -o gtest  | Changes the output formatting.<br>This may be clearer if you are using print statements for debugging.

For convenience, when the test script is run without any parameters, it also runs the linter at the end.

### Running the benchmarks suite

To test the performance of the generated code, this code repository also includes some benchmarks.
You can manually run one benchmark at a time to see how long it takes; currently there isn't a script to run them all at once.

To run of the benchmarks in the benchmarks directory, use the `benchmarks/run` script while being in the root project directory:

```sh
./benchmarks/run benchmarks/sieve/pallene.pln
```

By default, this will output the running time, as measured by `/usr/bin/time`.
But you can also measure the time using other tools.
`--mode=perf` shows [perf](https://en.wikipedia.org/wiki/Perf_%28Linux%29) output.
The `--mode=none` flag shows the stdout produced by the benchmark, without measuring the time.

```sh
./benchmarks/run benchmarks/sieve/pallene.pln --mode=none
```

To run Pallene's benchmarks you need to have /usr/bin/time installed in your system.
Some Linux distributions may have only the Bash time builtin function but not the /usr/bin/time executable.
If that is the case you will need to install time with `sudo apt install time` or equivalent.

To run benchmarks with LuaJIT, use the `--lua` option:

```sh
./benchmarks/run benchmarks/sieve/lua.lua --lua=luajit
```

If you change the ".pln" file of a benchmark, or if you change our Pallene-to-Lua translator in "translator.lua",
please run the `./benchmarks/generate_lua` script to regenerate the ".lua" files for the benchmarks.

# Git workflow

The `misc/` directory contains some scripts that I find useful in my day-to-day interactions with Git.
They are not required, but maybe you'll also find them useful.

The `pre-commit` hook ensures that you never commit directly to the master branch,
and that pull requests are always prepared on a separate branch.

The `git-sync` script pulls the latest changes from the upstream repository.
It updates all the local branches and it deletes the local copy of the branches,
if the pull request they are from has been merged.
If you copy this script to your `$PATH`, you can call it using `git sync`.
