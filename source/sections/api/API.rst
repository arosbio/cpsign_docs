.. _api:

.. |br| raw:: html

   <br />


Application Programming Interface
=================================

CPSign can, apart from the CLI-tool, be used as an API (Application Programming Interface) 
and be integrated in other projects and be called programmatically. The API consists of a 
factory-class that give access to a set of interfaces and classes open to use, provided that 
the user has a license for the product.

.. toctree::
   :maxdepth: 1
   
   /sections/api/scoring_algorithms
   /sections/api/predictors
   /sections/api/signature_predictors

   
.. contents:: Table of Contents
   :depth: 3


Programming examples
--------------------

For the fastest way to get started using CPSign API, please refer to our GitHub_ page with programming 
examples that get you up and running in no time!

.. _GitHub: https://github.com/arosbio/cpsign-examples 


Setting up Javadoc
------------------

:ref:`When using the API, it's always nice the have some help right in your favorite IDE, click 
on this link for a short guide for setting up javadac in Eclipse <javadoc_setup>`


Accessing the API
-----------------

Using the API requires a license, just as the CLI does. To get access to the API, the factory class :code:`CPSignFactory` must be instantiated. 
The constructors of this class handles the license-verification and permissions. The permissions are the same as for the CLI, a **Predict** license 
can only load existing models, whereas **Standard** and **Pro** licenses have full permissions to see and do everything. Trying to access methods that require
full permission and without having them will typically result in an :code:`InvalidLicenseException`. There are five constructors:

.. code-block:: java

   // Single license
   CPSignFactory factory = new CPSignFactory(CPSignLicense);
   CPSignFactory factory = new CPSignFactory(URI); 
   CPSignFactory factory = new CPSignFactory(URI, String);
   // Two licenses
   CPSignFactory factory = new CPSignFactory(URI, URI);
   CPSignFactory factory = new CPSignFactory(URI, URI, String);

The first constructor is typically only for internal usage, the other four is more for API users.
These four constructors are for two types of flavors, but each flavor has the optional 
PIN that can be supplied in case you have a YubiKey that has a unique PIN-code. |br| 

2-3. These ares the standard constructors that can take any type of license and in 
case your license supports encryption the API will give access to encrypting models (see :ref:`Encryption section <api_encryption>`). |br|  
4-5. These constructors are for passing a Pro-license as the first argument 
(which give access to training models or generating datasets). The second argument is the 
encryption-license, the license that datasets and models should be encrypted to. 
This means that the second argument must be either a Predict-license or another Pro-license. 
In case you have a unique PIN to your YubiKey that is connected to the encrypt-license, you 
call the constructor that allows the optional PIN. |br| 


Configure logging
-----------------

CPSign uses slf4j_ and logback_ internally which can be configured by any api user. The default Levels are set to Level.INFO
for printing information to System.out and Level.ERROR for printing to System.err. If you for instance wish to totally disable any
output from cpsign, simply set the appropriate levels:

.. _slf4j: http://www.slf4j.org/
.. _logback: http://logback.qos.ch/

.. code-block:: java
   
   // Get the root logger for cpsign
   ch.qos.logback.classic.Logger cpsignRoot = (ch.qos.logback.classic.Logger) org.slf4j.LoggerFactory.getLogger("com.arosbio");
   // Disable all output
   cpsignRoot.setLevel(ch.qos.logback.classic.Level.OFF);


Parameter tuning and Grid Search
--------------------------------
Parameter tuning is supported using the :code:`GridSearch` class which does an exhaustive search of parameter values:

.. code-block:: java
   
   // Create a GridSearch object
   GridSearch gs = new GridSearch(cvFolds, confidence, tolerance);
   // or new GridSearch() with default values
   
   // Set your custom parameter regions
   gs.setC(new ParameterRange(-1, 15, 2)); // Either using the start, stop, step parameters
   gs.setC(new ParameterRange(Arrays.asList(50., 100., 150.))); // Or use explicit values
   
   // Set a Writer to write all output to (otherwise only give you the optimal result)
   Writer writer = new OutputStreamWriter(System.out);
   gs.setWriter(writer);
   
   // GridSearch can either be called on the "Signatures level"
   SignaturesCPClassification signACP = ...;  // Init and load data
   GridSearchResult res = gs.classification(signACP, OptimizationType.EFFICIENCY);
   
   // or at the "Problem level"
   ACPClassification acp = ...; // Init with ML algorithm and Sampling strategy 
   Problem problem = ...; // Load data
   GridSearchResult res = gs.classification(problem, acp, OptimizationType.EFFICIENCY);
   
   
The default behaviour of the GridSearch is to only give the optimal values in the produced GridSearchResult, but all 
data can be outputted in case you give a Writer_ to the GridSearch (or alternatively configure the GridSearch logging-level to DEBUG).

.. _Writer: https://docs.oracle.com/javase/7/docs/api/java/io/Writer.html

.. _api_encryption:

Encrypting models and data in the API
-------------------------------------

Encrypting/decrypting models are done when saving and loading models. Encryption requires a license that supports it, 
from the license an :code:`EncryptionSpecification` can be generated by CPSign, which then allows encryption using the API. 
The :code:`EncryptionSpecification` objects are instantiated from the :code:`CPSignFactory`, either by sending the desired 
encryption-enabled license to the constructor of :code:`CPSignFactory` or by calling one of the static methods 
:code:`CPSignFactory.getEncryptionSpec(license)` or :code:`CPSignFactory.getEncryptionSpec(license, yubikePIN)`. 
Once having the encryption specification the models can be encrypted by calling the saving methods 
:code:`saveEncrypted(File, EncryptionSpecification)` or directly one of the :code:`ModelCreator` methods. 
Loading an encrypted model is done by sending the same :code:`EncryptionSpecification` (i.e. derived from the same license)
to the loading method of :code:`ModelLoader`. 


Loading data from any datasource
--------------------------------

Apart from loading molecules from SMILES-files and SDFiles CPSign offers a more generic way 
of populating a Signatures problem by the method :code:`fromMolsIterator` that allows the 
API-user to wrap their datasource in a Java-Iterator. In this way, CPSign will integrate 
to any type of datasource; whether it’s a database or anything else. 
The API-user simply wraps their data in an Iterator<Pair<IAtomContainer, Double>> 
where in the regression-case the second value in the pair will be the regression-value 
and in the classification-case the second value should be one of two values (one value for each class). 
The molecules must come as a IAtomContainer as implemented in CDK (https://github.com/cdk/cdk). 

