
(cli)=

# Using CPSign from command line

CPSign can be used for number of different task within machine learning and compound chemistry. The standard flow of action are these steps:

1. Use {ref}`Precompute <precompute>` to compute descriptors from input chemical data, yielding a `precomputed dataset`.
2. (*Optional*) Use {ref}`Tune <tune>` or {ref}`Tune-scorer <tune-scorer>` to find optimal hyperparameters for your particular data (otherwise default parameters are used according to {ref}`[6] <refs>`).
3. Use {ref}`Train <train>` to train the predictor (optional for TCP - where the {ref}`Online-predict <online-predict>` program can be used instead)
4. Use {ref}`Predict <predict>` to predict new compound(s).
5. (*Optional*) Use {ref}`Validate <validate>` to validate the model, if you have an external test/validation-set.

The available CPSign programs are:

```{toctree}
:maxdepth: 1

/sections/cli/precompute
/sections/cli/train
/sections/cli/aggregate
/sections/cli/fast-aggregate
/sections/cli/predict
/sections/cli/online-predict
/sections/cli/tune
/sections/cli/tune-scorer
/sections/cli/crossvalidate
/sections/cli/validate
/sections/cli/gensign
/sections/cli/check-version
/sections/cli/explain
```

This page contains the general options. For each specific program, please follow the links above or use the navigation on the left.

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Running CPSign
Running `CPSign` on the command line requires having the "Uber JAR" (i.e. the Java archive including all dependencies). This jar is a [really executable JAR](https://github.com/brianm/really-executable-jars-maven-plugin), meaning that it can be called as a program on the command line using a `./cpsign-[version]-uber.jar` type call on linux/mac systems. This internally calls `java -jar <jvm arguments> <cpsign-[version]-uber.jar>`. Depending on how you download the jar, you may have to change file permissions using e.g. `chmod` in order allow it to be executed. 

You may also use a standard `java -jar <jvm arguments> cpsign-[version]-uber.jar` call in order to give custom jvm arguments, e.g. in case you need to give the jvm more memory if you have a large data set. For simplicity, this web site will use the shorter `./cpsign-[version]-uber.jar` in all examples, but all these can be replaced with the longer `java -jar ..` invocation instead. 

## Get usage help

Usage information can be accessed both at the top level, which will give information about which program commands that are accepted:

```bash
> ./cpsign-[version]-uber.jar
``` 

or for each program, here exemplified for the `precompute` command:

```bash
> ./cpsign-[version]-uber.jar precompute
``` 

### Detailed explanation of parameters using the `explain` program
Some flags and input require more information than can easily fit in the usage help page of individual programs, such information can instead be viewed using the `explain` program:

```bash
> ./cpsign-[version]-uber.jar explain
```

Running the above will list the available topics where more information can be retrieved.

## Giving multiple arguments for a single parameter

For some parameters, such as classification labels or grid parameters in tune etc, you will have to pass multiple arguments for a single flag. This is done fairly straightforwardly by separating the arguments by either a whitespace character (blank space, tab etc), a comma (,) or both. If you have a text argument that already contain any of these characters you wrap the arguments in quotation-marks, e.g. `--labels "positive class" "negative class"`.

## Fuzzy-matching and case-insensitive arguments

All parameter flags need to match to be written 100% correctly, but the arguments for the flags can in most cases be shortened or misspelled (to some degree). This can in some cases lead to unexpected results if the argument happened to have a better "fuzzy score" with some other argument. We thus recommend that you write out the arguments correctly each time, in case you have unexpected failures or behavior, make sure to check your arguments.

CPSign is also case-insensitive for most arguments, but not for the flags and program names.

## Use a configuration file

The command line tool supports **@syntax**, meaning that all parameters can be specified in files so that you do not have to rewrite all parameters for each run. You can also use multiple files and mix file and plain arguments on the command line.

**Note:** The program argument (e.g. `train` or `predict`) always have to come as the first parameter, regardless if parameters are specified in a configuration file or as parameters passed directly.

train_config.txt:

```text
train
--predictor-type 2
--data-set <path>
--time
...
```

The path specified to the **@** symbol can either of the following:

- An absolute path (e.g. `/Users/username/Documents/runconfigs/config.txt` on Unix/Linux systems or
  `C:\\Users\\User\\CPSign\\runconfigs\\config.txt` on Windows systems)
- A relative path to the **current directory** that the java -jar command is executed in (e.g. `runconfigs/config.txt` or
  `..\\CPSign\\runconfigs\\config.txt`)
- On Unix/Linux based systems the path can also be relative to user home (i.e. using `~/Documents/runconfigs/config.txt`)

Example in bash:

```bash
> # An absolute path
> ./cpsign-[version]-uber.jar \
        @/Users/username/runconfigs/train_config.txt \
        [other options]

> # A user home relative path
> ./cpsign-[version]-uber.jar \
        @~/runconfigs/train_config.txt \
        [other options]

> # A relative path
> ./cpsign-[version]-uber.jar \
        @../runconfigs/train_config.txt \
        [other options]
```

Example in Windows Command prompt

```bash
:: Use absolute path (windows style)
C:\Users\User\CPSign> java -jar cpsign-1.0.0.jar @C:\Users\User\CPSign\runconfigs\train_config.txt

:: Use absolute path (*nix style)
C:\Users\User\CPSign> java -jar cpsign-1.0.0.jar @/Users/user/CPSign/runconfigs/train_config.txt

:: Use a relative path (forward slashes)
C:\Users\User\CPSign> java -jar cpsign-1.0.0.jar @../CPSign/runconfigs/train_config.txt

:: Use a relative path (back slashes)
C:\Users\User\CPSign> java -jar cpsign-1.0.0.jar @..\CPSign\runconfigs\train_config.txt
```

## Configure Logging & Output

CPSign will by default write information on the screen and to a rather verbose log file that will be written in the same directory of where CPSign is run from. However, you can configure CPSign to run in **silent mode** using the `-q, --quiet, --silent` flag, meaning that no output will be written on the screen (except if the run fails for some reason, e.g. bad input, the error messages will be printed to the system error stream). If you do not want to generate a log file this can be achieved by giving the `--no-logfile` flag, or if you wish to write the log in a separate location, e.g. having a separate log for each run, this is configured using the `--logfile <path>` parameter.


## :-syntax

To reduce the number of parameters-flags in CPSign and to create a more natural grouping of arguments, CPSign uses ':-syntax' for many of its arguments. What this means is that sub-parameters are specified together with the parameter itself by separating the arguments and subsequent sub-arguments with a `:` character. E.g. when specifying the scorer-implementation and its unique arguments such as kernel-type, kernel-parameters, cost, epsilon, etc. The available sub-arguments are specific for each scorer-implementations and can be retrieved from the corresponding help-menu. 

The :-syntax can either be specified with the sub-parameters explicitly named or by using the order of the parameters. The general usage is either (in explicit form):

`
<param-flag> <main-argument>:<sub-param-1-name>=<sub-param-1-value>:<sub-param-2-name>=<sub-param-2-value>
`

or, if the order of the sub-parameters is known:

`
<param-flag> <main-argument>:<sub-param-1-value>:<sub-param-2-value>
`

In the first case, the order of the parameters is not important, whereas in the second, short hand syntax, the order is critical. Mixing of explicit and short hand arguments is allowed as long as the short hand parameters all come before any argument in explicit form (otherwise the order is ambiguous). Note that the order of sub-parameters may change between versions of CPSign so the explicit version should be preferred for setting up scripts that can be used over a longer time.

### Example
Consider the example of setting the scorer to be `LinearSVC` and setting the parameters `cost` and `epsilon` to 100 and 0.01, respectively. Running `explain scorer` shows that cost is the first parameter and epsilon is the second, thus the following arguments are identical.

```bash
> [other parameters] --scorer LinearSVC:100:0.01
> [other parameters] --scorer LinearSVC:epsilon=0.01:cost=100
> [other parameters] --scorer LinearSVC:100:epsilon=0.01
```


## Encryption

When data should be encrypted, that is told to CPSign by passing the `--encrypt` flag. What type (one- or two-factor) is handled by the license
itself, either you have a license configured for one-factor encryption or two-factor. Of course, if two-factor encryption should be used, the
physical encryption key must be connected to your system. When accessing encrypted data, may it be precomputed data or models, the
CPSign will handle that without any supplied flags, but requires the correct license to be supplied to the `--license` flag (the license that
the data has been encrypted for and the physical encryption key if two-factor encryption is used).

Flag: `--encrypt` \[path to license to encrypt data for\] 
Description: Specify that generated data should be encrypted after the given license. Note that a Standard or PRO license must be supplied to
the `--license` flag for having access to this functionality.

Example encrypting for a Predict-license:

```bash
> java -jar cpsign-[version].jar [precompute or train] \
   --license /path/to/valid/pro/or/standard/license.license \
   --encrypt /path/to/valid/predict.license \
   [other options]
```

Now the generated signatures and data/models are encrypted after the predict.license file, accessing that data can only be done by supplying
the same predict license next time. Note that the Standard or PRO license is only supplied for getting privileges for precomputing or training,
but that license cannot decrypt the data:

```bash
> java -jar cpsign-[version].jar [predict] \
   --license /path/to/predict.license \
   [other options]
```

## "Three-factor" Encryption

Two-factor encryption also comes with the option to have a non default PIN-code (i.e. "Three-factor" encryption). If you have a non-default
PIN, this must be supplied both when generating encrypted data and when accessing it.

Flag: `--two-factor-pin` 
Description: Pass the flag without any argument to it, the program will prompt you to enter the PIN when it starts

```bash
> java -jar cpsign-[version].jar [precompute or train] \
   --license /path/to/valid/pro/or/standard/license.license \
   --encrypt /path/to/valid/predict.license \
   --two-factor-pin \
   [other options]
   If two-factor encryption is used and key has a non-default PIN: [enter your pin here]
```

## Generating model jars

CPSign will store all models as jar-files. The following information/parameters are plausible to add to the model:

```bash
Output:
  -mo | --model-out                        [path]
     Model file to generate (--model-out or --model-out-dir are required to pass)
  --model-out-dir                          [path]
     Specify a directory where the model should be saved, leave naming to cpsign
     (--model-out or --model-out-dir are required to pass). Specify '.' if model should
     be generated in the current directory.
* -mn | --model-name                       [text]
     The name of the model
  -mc | --model-category                   [text]
     The category of the model, will end up as model-endpoint in the model JAR
  -mv | --model-version                    [text]
     Optional model version in SemVer versioning format
     Default: 1.0.0_{date-time-string}
```

Either of the `--model-out` or `--model-out-dir` are required to send. `--model-out` is an explicit model to generate,
whereas `--model-out-dir` can be used to specify a directory where the model should be saved and CPSign will generate a
model file name for you.

## Progress bar

CPSign write out text continuously during each run, except in case it is silenced by the user. In case a graphical view
of the progress status is desired, it's possible to add the `--progress-bar` or `--progress-bar-ascii` flags.
Using `--progress-bar` outputs the progress in Unicode format, which is poorly rendered in case
it is not supported by the terminal or environment used, i.e. in case output is viewed using a text-file in retrospect,
in that case there is the ASCII progress bar to fall back on (`--progress-bar-ascii`).
If both using the progress bar option and getting all text output at the same time, the output will be messy,
in that case the standard out (normal cpsign text) should be separated from the standard error (progress bar).
This can be done e.g. by sending standard out to a text file and only view the progress bar in the terminal, by using i.e.
{code}`> java -jar cpsign-[version].jar train ..... > log.txt` which then only leave standard error text to be sent
to the terminal, thus separating the output so it is displayed correctly.

Note that the progress bars are non-conflicting with the `--silent` flag, so it is fully possible to ignore
the standard output and only display the progress bar.

## Exit status codes

From v 0.6.1 cpsign will exit with non-zero exit codes in case of failure during execution. The exit codes are the following:

```{eval-rst}

:0: Successful execution
:1: Faulty command
:2: Faulty arguments
:4: Missing permissions
:8: Out of memory
:16: Program error
```

[alias]: http://www.linfo.org/alias.html
