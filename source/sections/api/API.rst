.. _api:

.. |br| raw:: html

   <br />

#################################
Application Programming Interface
#################################

CPSign can, apart from the CLI-tool, be used as an API (Application Programming Interface) and be integrated in other projects and be called programmatically. The API consists of a factory-class that give access to a set of interfaces and classes open to use, provided that the user has a license for the product.

.. toctree::
   :maxdepth: 1
   
   /sections/api/scoring_algorithms
   /sections/api/predictors
   /sections/api/signature_predictors

   
.. contents:: Table of Contents
   :depth: 3


Programming examples
====================

For the fastest way to get started using CPSign API, please refer to our `GitHub <https://github.com/arosbio/cpsign-examples>`_ page with programming 
examples that get you up and running in no time!


Setting up Javadoc
==================

:ref:`When using the API, it's always nice the have some help right in your favorite IDE, click 
on this link for a short guide for setting up javadac in Eclipse <javadoc_setup>`


Accessing the API
=================

Using the API requires a license, just as the CLI does. To get access to the API, the factory class :code:`CPSignFactory` must be instantiated. The constructors of this class handles the license-verification and permissions. The permissions are the same as for the CLI, a **Predict** license can only load existing models, whereas **Standard** and **Pro** licenses have full permissions to see and do everything. Trying to access methods that require full permission (and without having them) will typically result in an :code:`InvalidLicenseException`. There are five constructors:

.. code-block:: java

   // Single license
   CPSignFactory factory = new CPSignFactory(CPSignLicense);
   CPSignFactory factory = new CPSignFactory(URI); 
   CPSignFactory factory = new CPSignFactory(URI, String);
   // Two licenses
   CPSignFactory factory = new CPSignFactory(URI, URI);
   CPSignFactory factory = new CPSignFactory(URI, URI, String);

The first constructor is typically only for internal usage, the other four is more for API users. These four constructors are for two types of flavors, but each flavor has the optional PIN that can be supplied in case you have a YubiKey that has a unique PIN-code. |br| 

2-3. These are the standard constructors that can take any type of license and in case your license supports encryption the API will give access to encrypting models (see :ref:`Encryption section <api_encryption>`). |br|  
4-5. These constructors are for passing a Pro-license as the first argument (which give access to training models or generating datasets). The second argument is the encryption-license, the license that datasets and models should be encrypted to. This means that the second argument must be either a Predict-license or another Pro-license. In case you have a unique PIN to your YubiKey that is connected to the encrypt-license, you call the constructor that allows the optional PIN. |br| 


Configure logging
=================

CPSign uses `SLF4J <http://www.slf4j.org/>`_ and `Logback <http://logback.qos.ch/>`_ for logging. Both logbacks own logging-output and CDK logging has been shut of using the included `logback.xml` file in the CPSign jar. This can be altered at your conventience. Note that instantiating the :code:`CPSignFactory` forces CDK to use SLF4J (it normally uses its own logging framework), which means that the user can configure CDK logging levels and appenders using logback, either by replacing the config file or programmatically. Note that the included logging config does not include any appenders, thus not creating any logfiles unless configured to by the API user.


Parameter tuning and Grid Search
================================
Parameter tuning is supported using the :code:`GridSearch` class which does an exhaustive search of parameter values. Calling any of the :code:`GridSearch.search(..)` methods kicks of the parameter tuning, either with default arguments or using a supplied parameter grid. Which parameters that can be tuned and their default (if any) tested values can be queried using the :code:`Configurable` interface at any required depth of the predictor. An example of how this can be done is found at `CPSign Examples <https://github.com/arosbio/cpsign-examples>`_ in `ParameterTuning.java`. Note that if your data set size is large, it can be convenient to use a random test-train split instead of the default 10-fold CV. 

.. code-block:: java
   
   // Create a GridSearch object
   GridSearch gs = new GridSearch(TestingStrategy, confidence, tolerance);
   // or new GridSearch() with default values

   // Configure the number of returned results (note these are ordered in terms of best performance)
   gs.setNumberOfResultsToReturn(int)
   // Or set a Writer to write output continously (in csv format)
   Writer writer = new OutputStreamWriter(System.out);
   gs.setLoggingWriter(writer);
   
   // Define your custom parameter regions
   Map<String,List<Object>> paramGrid = new HashMap<>();
   paramGrid.put("cost", Arrays.asList(1, 10, 100));
   // Grid search beta only if NCM uses it! 
   paramGrid.put("ncmBeta", Arrays.asList(0.0, 0.25, 0.5));
   
   // GridSearch can either be called on the "Signatures level"
   SignaturesCPClassification signACP = ...;  // Init and load data
   GridSearchResult res = gs.search(signACP, new ObservedFuzzyness());
   
   // or at the "Problem level"
   ACPClassification acp = ...; // Init with ML algorithm and Sampling strategy 
   Problem problem = ...; // Load data
   GridSearchResult res = gs.search(problem, acp, new ObservedFuzziness());
   


.. _api_encryption:

Encrypting models and data in the API
======================================

Encrypting/decrypting models are done when saving and loading models. Encryption requires a license that supports it, from the license an :code:`EncryptionSpecification` can be generated by CPSign, which then allows encryption using the API. The :code:`EncryptionSpecification` objects are instantiated from the :code:`CPSignFactory`, either by sending the desired encryption-enabled license to the constructor of :code:`CPSignFactory` or by calling one of the static methods :code:`CPSignFactory.getEncryptionSpec(license)` or :code:`CPSignFactory.getEncryptionSpec(license, yubikePIN)`. Once having the encryption specification the models can be encrypted by calling the saving methods of :ref:`sign_predictors`. :code:`saveEncrypted(File, EncryptionSpecification)` or directly one of the :code:`ModelCreator` methods. Loading an encrypted model is done by sending the same :code:`EncryptionSpecification` (i.e., derived from the same license) to the loading method of :code:`ModelLoader`. 


Loading data from any datasource
================================


Apart from loading molecules from SMILES-files and SDFiles CPSign offers a more generic way of populating a Signatures problem by the method :code:`fromMolsIterator` that allows the API-user to wrap their datasource in a Java-Iterator. In this way, CPSign will integrate  to any type of datasource; whether it’s a database or anything else. The API-user simply wraps their data in an :code:`Iterator<Pair<IAtomContainer, Double>>` where in the regression-case the second value in the pair will be the regression-value and in the classification-case the second value should be one of two values (one value for each class). The molecules must come as a IAtomContainer as implemented in CDK (https://github.com/cdk/cdk). 

