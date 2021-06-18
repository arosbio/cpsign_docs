.. _cli: 
 
.. |br| raw:: html

   <br />


Using CPSign from command line
==============================

CPSign can be used for number of different task within machine learning and compound chemistry. The standard flow of action are these steps:

1. (*Optional*) Use :ref:`Tune <tune>` to find optional SVM settings for your dataset (otherwise good default ones are used according to :ref:`[6] <refs>`).
2. (*Optional*) Use :ref:`Precompute <precompute>` to convert chemical file format into a sparse numerical representation.
3. Use :ref:`Train <train>` to train the predictor (not required for TCP, but greatly speeds up the prediction time!).
4. Use :ref:`Predict <predict>` to predict new compound(s).
5. (*Optional*) Use :ref:`Validate <validate>` to validate the model, if you have an external test-set.

The available CPSign programs are:

.. toctree::
   :maxdepth: 1
   
   /sections/cli/precompute
   /sections/cli/train
   /sections/cli/aggregate
   /sections/cli/fast-aggregate
   /sections/cli/predict
   /sections/cli/online-predict
   /sections/cli/tune
   /sections/cli/crossvalidate
   /sections/cli/validate
   /sections/cli/gensign
   /sections/cli/check-version
   /sections/cli/check-license
   /sections/cli/explain
   
   

This page contains the general options. For each specific program, please follow the links above or use the
navigation on the left.

.. contents:: Table of Contents
   :depth: 3
   :backlinks: top


Get usage help
--------------
Usage information can be accessed both at the top level, which will give 
information about which program commands that are accepted:

.. code-block:: bash
   
   > java -jar cpsign-[version].jar
   ...
   The following main program commands are accepted:
         precompute
         train
         aggregate
         fast-aggregate
         predict
         online-predict
         tune
         crossvalidate
         validate
         gensign
         version
         check-version
         check-license
         explain
      
   To see the parameters for each program, run the command:

      java -jar cpsign-1.0.0.jar [program]
   

Usage help is splitted up for each program. Required parameters are marked with an asterisk (*) before the parameter. All
parameters that take arguments has the required type of the argument specified after the parameter-flags. Example: 

.. code-block:: bash
   
   > java -jar cpsign-[version].jar precompute -h 
   
      ...
   
      General:
     * --license                                [URI | path]
          Path or URI to license file
       -h  | --help | man
          Get help text
       --short
          Use shorter help text (used together with the --help argument)
       --logfile                                [path]
          Path to a user-set logfile, will be specific for this run
       --silent
          Silent mode (only print output to logfile)
       --echo
          Echo the input arguments given to CPSign
       --seed                                   [integer]
          Set this flag if an explicit RNG seed should be used in tasks that require a RNG
          (randomization of training data, splitting in cross-validation, learning algorithms
          etc). Not used by all programs.
       --progress-bar
          Add a Progress bar in the system error output
       --progress-bar-ascii
          Add a Progress bar in ASCII in the system error output
       --time
          Print wall-time for all individual steps in execution
           
      ...

So the flag ``--license`` takes either a **URI** (Unified Resource Identifier) or a **path** which is a standard file-path
on your computer. Parameters that are not followed by **"[type]"** are flags that does not accept any further arguments to be passed.

Note that the help-text will be written either by omitting to pass any argument after the **"[program]"**, or by adding the -h/\\--help/man flag.

Less verbose help
^^^^^^^^^^^^^^^^^
The help/manual text is rather verbose, you can opt for a only getting the parameters and not the help text for each parameter by
adding the ``--short`` flag:

.. code-block:: bash
   
   > java -jar cpsign-[version].jar precompute -h --short
   
      ...
      
      General:
     * --license                                [URI | path]
       -h  | --help | man
       --short
       --logfile                                [path]
       --silent
       --echo
       --seed                                   [integer]
       --progress-bar
       --progress-bar-ascii
       --time
           
      ...

Parameters for a single section
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
In case you wish to only get parameters for a given section of parameters, let us say the input, then 
you can add "input" after the program as follows:

.. code-block:: bash
   
   > java -jar cpsign-[version].jar precompute input -h --short
   
                                           precompute
    ------------------------------------------------------------------------------------------
      
      Input:
        -mt | --model-type                       [id | text]
        -td | --train-data                       [URI | path]
        -e  | --endpoint                         [text]
        -l  | --labels                           [label label]


Giving multiple arguments for a single parameter
--------------------------------------------------
For some parameters, such as classification labels, possible SVM parameters to try out in tune etc, you will have
to pass multiple arguments for a single flag. This is done fairly straightforwardly by separating the arguments
by either a whitespace character (blank space, tab etc), a comma (,) or both. If you have a text argument that already 
contain any of these characters you wrap the arguments in quotation-marks, e.g. ``--labels "positive class" "negative class"``.     


Fuzzy-matching and case-insensitive arguments
---------------------------------------------
All parameter flags need to match to be written 100% correctly, but the arguments for the flags can in most cases be
shortened or misspelled (to some degree). This can in some cases lead to unexpected results if the argument happened
to have a better "fuzzy score" with some other argument. We thus recommend that you write out the arguments correctly each time,
in case you have unexpected failures or behavior, make sure to check your arguments. 

CPSign is also case-insensitive, both for parameter flags and their arguments.     


Use a configuration file
------------------------
The command line tool supports **@syntax**, meaning that all parameters can be specified in files so that you do not have to rewrite 
all parameters for each run. You can also use multiple files and mix file and plain arguments on the command line. Note that each
parameter must come on a new line, even the flag and it's argument must be separated by a new line

**Note** The command name (i.e. train, predict etc.) always have to come as the first parameter, regardless if parameters are specified in
a configuration file or as parameters passed directly. 

train_config.txt::

	train
	--license
	/path/to/cpsign0.3-standard.license
	--predictor-type
	2
	--calibration-ratio
	0.1
	--nr-models
	10
	--time
	...

The path specified to the **@** symbol can either of the following:

- 	An absolute path (e.g. `/Users/username/Documents/runconfigs/config.txt` on Unix/Linux systems or 
	`C:\\Users\\User\\CPSign\\runconfigs\\config.txt` on Windows systems)
-	A relative path to the **current directory** that the java -jar command is executed in (e.g. `runconfigs/config.txt` or 
	`..\\CPSign\\runconfigs\\config.txt`)
-	On Unix/Linux based systems the path can also be relative to user home (i.e. using `~/Documents/runconfigs/config.txt`)

Example in bash:

.. code-block:: bash
	
	> # An absolute path
	> java -jar cpsign-[version].jar \
		@/Users/username/runconfigs/train_config.txt \
		[other options]
		
	> # A user home relative path
	> java -jar cpsign-[version].jar \
		@~/runconfigs/train_config.txt \
		[other options]
	
	> # A realtive path
	> java -jar cpsign-[version].jar \
		@../runconfigs/train_config.txt \
		[other options]

Example in Windows Command prompt

.. code-block:: bash

	:: Use absolute path (windows style)
	C:\Users\User\CPSign> java -jar cpsign-1.0.0.jar @C:\Users\User\CPSign\runconfigs\train_config.txt
	
	:: Use absolute path (*nix style)
	C:\Users\User\CPSign> java -jar cpsign-1.0.0.jar @/Users/user/CPSign/runconfigs/train_config.txt
	
	:: Use a relative path (forward slashes)
	C:\Users\User\CPSign> java -jar cpsign-1.0.0.jar @../CPSign/runconfigs/train_config.txt
	
	:: Use a relative path (back slashes)
	C:\Users\User\CPSign> java -jar cpsign-1.0.0.jar @..\CPSign\runconfigs\train_config.txt



Get jar version
---------------
To get the unique CPSign jar-version including build-tag, that is more specific than the major.minor.patch version, there is now a 
program ``version`` that returns the version of CPSign.

.. code-block:: bash

   > java -jar cpsign-1.0.0.jar version

    CPSign - Conformal Prediction with the signatures molecular descriptor.
    © 2019, Aros Bio AB, www.arosbio.com
    Version: 1.0.0.20190829_144526



Program aliases
---------------
Some of the programs has aliases, to see potential aliases you simply add **aliases** after the program name:

.. code-block:: bash

   > java -jar cpsign-0.7.0.jar crossvalidate aliases
   
   Aliases for program crossvalidate:
	   cv
	

Shorten CLI calls
-----------------
Consider using an alias_ to shorten the CLI calls, i.e. so that :code:`java -jar cpsign-[version].jar` is not 
needed to be written each time. For instance you can do this at the beginning of each bash-script
by adding e.g. :code:`RUN="java -jar cpsign-[version].jar"` and then reuse it when running programs, calling e.g. train
by :code:`$RUN train ....`. If several scripts are used, and from different directories, it might be better to setup an alias_ 
using :code:`alias cpsign="java -jar /full/path/to/jar/cpsign-[version].jar"` and then use it anywhere with :code:`cpsign train ...`. 


.. _alias: http://www.linfo.org/alias.html

Configure Logging & Output
--------------------------
CPSign is by default configured to write information on the screen and to a rather verbose log-file that will be written in the same
directory of the jar file. However, you can configure CPSign to run in **silent mode**, meaning that no output will be written on the screen.
If you desire to have a separate logfile for each run or if the user does not have write-access to the directory where the CPSign-jar is located, 
it's also possible to configure this.

Flag: ``--silent`` |br|
Description: Disables output written to screen, will only be written to the cpsign.log file.

.. code-block:: bash

   > java -jar cpsign-[version].jar [program] --silent [other options]

Flag: ``--logfile`` |br|
Description: Supply the path of a per-run specific log-file. OBS the logfile with overwrite old files if they already exists. This
option also removes the logging to the standard cpsign.log and only logs to the specified file.

.. code-block:: bash

   > java -jar cpsign-[version].jar [program] --logfile /path/to/logfile.log [other options]


Using a license file
--------------------
You need a valid license file in order to run CPSign.

.. code-block:: bash

   > java -jar cpsign-[version].jar [program] \
      --license /path/to/Standard-license.license \
      [other options]


Selecting predictor type
------------------------
For programs like :ref:`train <train>` and :ref:`tune <tune>`, the predictor type must be given.

Flag:    ``-pt`` | ``--ptype`` | ``--predictor-type`` |br|
Options:

Predictor type:
 * (1) ACP Classification
 * (2) ACP Regression
 * (3) TCP Classification
 * (5) CVAP Classification

Default: 1

Example:

.. code-block:: bash

   > java -jar cpsign-[version].jar [program] -pt 2 [other options]


Selecting modeling implementation
---------------------------------
Apart from selecting predictor type, you also have to select the underlying modeling implementation that 
should be used (also called scoring algorithm). 
 
Flag:    ``-i`` | ``--impl`` |br|
Options:
 * (1) LibLinear
 * (2) LibSvm
 * (3) ProbabilisticLibSvm

Default: 1

Example (use libsvm instead of liblinear):

.. code-block:: bash

   > java -jar cpsign-[version].jar [program] -i libsvm [other options]

Signature heights
-----------------
Flag:    ``-hs`` | ``--height-start`` |br|
Description: Signatures start height (Default: 1) |br|
Flag:    ``-he`` | ``--height-end`` |br|
Description: Signatures end height (Default: 3) |br|

Example (set signatures heights ranging from 0 to 4):

.. code-block:: bash

   > java -jar cpsign-[version].jar [program] --height-start 0 --height-end 4 [other options]

Modeling endpoint
-----------------
Flag:    ``-e`` | ``--endpoint`` |br|
Description: Endpoint property that should be used for modeling (the endoint of the model). Should match a property in the train file.
Example:

.. code-block:: bash

   > java -jar cpsign-[version].jar [program] --endpoint "Ames test categorisation" [other options]

Classification labels
---------------------
Flag:    ``-l`` | ``--labels`` |br|
Description: Label(s) for endpoint values in classification mode. The class labels should correspond to labels used in the data file(s). Labels could
be either textual or numerical values.

Example:

.. code-block:: bash

   > java -jar cpsign-[version].jar [program] -l nonmutagen,mutagen [other options] 


Encryption
----------
When data should be encrypted, that is told to CPSign by passing the ``--encrypt`` flag. What type (one- or two-factor) is handled by the license
itself, either you have a license configured for one-factor encryption or two-factor. Of course, if two-factor encryption should be used, the
physical encryption key must be connected to your system. When accessing encrypted data, may it be precomputed data or models, the
CPSign will handle that without any supplied flags, but requires the correct license to be supplied to the ``--license`` flag (the license that
the data has been encrypted for and the physical encryption key if two-factor encryption is used).

Flag: ``--encrypt`` [path to license to encrypt data for] |br|
Description: Specify that generated data should be encrypted after the given license. Note that a Standard or PRO license must be supplied to
the ``--license`` flag for having access to this functionality.

Example encrypting for a Predict-license:

.. code-block:: bash

   > java -jar cpsign-[version].jar [precompute or train] \ 
      --license /path/to/valid/pro/or/standard/license.license \
      --encrypt /path/to/valid/predict.license \
      [other options]
      
Now the generated signatures and data/models are encrypted after the predict.license file, accessing that data can only be done by supplying
the same predict license next time. Note that the Standard or PRO license is only supplied for getting privileges for precomputing or training, 
but that license cannot decrypt the data:

.. code-block:: bash
   
   > java -jar cpsign-[version].jar [predict] \
      --license /path/to/predict.license \
      [other options]

"Three-factor" Encryption
-------------------------
Two-factor encryption also comes with the option to have a non default PIN-code (i.e. "Three-factor" encryption). If you have a non-default 
PIN, this must be supplied both when generating encrypted data and when accessing it.

Flag: ``--two-factor-pin`` |br|
Description: Pass the flag without any argument to it, the program will prompt you to enter the PIN when it starts

.. code-block:: bash

   > java -jar cpsign-[version].jar [precompute or train] \ 
      --license /path/to/valid/pro/or/standard/license.license \
      --encrypt /path/to/valid/predict.license \
      --two-factor-pin \
      [other options]
      If two-factor encryption is used and key has a non-default PIN: [enter your pin here]
 
 
Generating model jars
-----------------------
CPSign will store all models as jar-files. The following information/parameters are plausible to add to the model:

.. code-block:: bash

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
   

Either of the ``--model-out`` or ``--model-out-dir`` are required to send. ``--model-out`` is an explicit model to generate,
whereas ``--model-out-dir`` can be used to specify a directory where the model should be saved and CPSign will generate a 
model file name for you. 

Progress bar
------------
CPSign write out text continuously during each run, except in case it is silenced by the user. In case a graphical view
of the progress status is desired, it's possible to add the ``--progress-bar`` or ``--progress-bar-ascii`` flags. 
Using ``--progress-bar`` outputs the progress in Unicode format, which is poorly rendered in case
it is not supported by the terminal or environment used, i.e. in case output is viewed using a text-file in retrospect, 
in that case there is the ASCII progress bar to fall back on (``--progress-bar-ascii``). 
If both using the progress bar option and getting all text output at the same time, the output will be messy,
in that case the standard out (normal cpsign text) should be separated from the standard error (progress bar).
This can be done e.g. by sending standard out to a text file and only view the progress bar in the terminal, by using i.e.
:code:`> java -jar cpsign-[version].jar train ..... > log.txt` which then only leave standard error text to be sent
to the terminal, thus separating the output so it is displayed correctly.

Note that the progress bars are non-conflicting with the ``--silent`` flag, so it is fully possible to ignore
the standard output and only display the progress bar.

Exit status codes
-----------------
From v 0.6.1 cpsign will exit with non-zero exit codes in case of failure during execution. The exit codes are the following:

:0: Successful execution
:1: Faulty command
:2: Faulty arguments
:4: Missing permissions
:8: Out of memory
:16: Program error 

