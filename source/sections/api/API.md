(api)=

# Application Programming Interface

CPSign can, apart from the CLI-tool, be used as an API (Application Programming Interface) and be integrated in other projects and be called programmatically. The API consists of a set of Maven modules which can be downloaded from [Maven Central](https://central.sonatype.com/namespace/com.arosbio) or built directly from the source code at [GitHub](https://github.com/arosbio/cpsign/).

```{toctree}
:maxdepth: 1

/sections/api/data_handling
/sections/api/scoring_algorithms
/sections/api/predictors
/sections/api/chem_predictors
```

```{contents} Table of Contents
:depth: 3
```

## Programming examples

For the fastest way to get started using CPSign API, please refer to our [GitHub](https://github.com/arosbio/cpsign-examples) page with programming examples that get you up and running in no time!

## Available modules
CPSign 2.0 is split into several maven child modules and a parent aggregation project. The modules are;
* **depict** - An extension of the CDK depictions code which allows for generating 'blooming' molecules - i.e. visually appealing depictions with highlights that fade of and mix. Intended mainly for displaying which atoms contributed the most in a given prediction.
* **fast-ivap** - An implementation of the algorithms detailed in [Vovk et al. 2015](http://alrw.net/articles/13.pdf) that pre-computes the isotonic regression used in the Venn-ABERS algorithm.
* **encrypt-api** - **Moved to a separate project, not a child module any more**. A single interface for including encryption of saved prediction models or precomputed data sets. Contact [Aros Bio](https://arosbio.com) in case you wish to purchase such an extension that secures models by only allowing predictions when also having the encryption key.
* **test-utils** - Project that include test resources such as SVMLight files and some QSAR datasets used in the tests.
* **confai** - Conformal and probabilistic predictors, data, processing etc, excluding CDK and chemistry specific code. Thus provides a software package that allows training CP and Venn-ABERS models for non-chemical data, without the overhead of including the complete CDK package.
* **cpsign-api** - Java API of CPSign, including chemistry and the CDK library.
* **cpsign** - Final CLI version of CPSign.

For standard users the **confai** and **cpsign-api** modules should be the ones of most interest, either supplying non-chemistry dependent code (confai) or wrapper/convenience code for dealing directly with chemical data (cpsign-api). 

## Using CPSign

CPSign is now available from [Maven Central](https://central.sonatype.com/namespace/com.arosbio) which makes it easy to include it using a package manager such as Maven or Gradle (which takes care of transitive dependencies of cpsign);

Including Conf-AI in your java code
```html
<dependency>
    <groupId>com.arosbio</groupId>
    <artifactId>confai</artifactId>
    <version>2.0.0-rc6</version>
</dependency>
```

Including CPSign-API in your code
```html
<dependency>
    <groupId>com.arosbio</groupId>
    <artifactId>cpsign-api</artifactId>
    <version>2.0.0-rc6</version>
</dependency>
```

## Configure logging

CPSign uses [SLF4J](http://www.slf4j.org/) and [Logback](http://logback.qos.ch/) for logging. Both logbacks' own logging-output and CDK logging has been shut of using the included `logback.xml` file in the **confai** module. This can be altered at your convenience. 

## Hyper-parameter tuning and Grid Search

Hyper-parameter tuning is supported using the `GridSearch` class which does an exhaustive search of parameter values. Calling any of the `GridSearch.search(..)` methods kicks of the parameter tuning, either with default arguments or using a supplied parameter grid. Which parameters that can be tuned and their default (if any) tested values can be queried using the `Configurable` interface at any required depth of the predictor hierarchy. An example of how this can be done is found at [CPSign Examples](https://github.com/arosbio/cpsign-examples) in `ParameterTuning.java`. Note that if your data set is large, it can be convenient to use a random test-train split instead of the default 10-fold CV, or using a stratified splitting strategy in case of imbalanced classification data.

```java
// If you wish to output progress continuously (in csv format), you can 
// supply a Writer instance to do this. 
Writer writer = new OutputStreamWriter(System.out);

// Create a GridSearch object using builder class
GridSearch gs = new GridSearch.Builder()
			.computeMeanSD(true)
			.confidence(confidence)
			.testStrategy(new KFoldCV(k))
			.tolerance(tol)
			.evaluationMetric(new ObservedFuzziness())
			// Set a Writer to write all output to (otherwise only give you the 'n' optimal results)
			.loggingWriter(writer)
            .maxNumResults(5) // e.g. only get the top 5 best results
			.build();

// Define your custom parameter regions
Map<String,List<Object>> paramGrid = new HashMap<>();
paramGrid.put("cost", Arrays.asList(1, 10, 100));
// Grid search beta only if NCM uses it!
paramGrid.put("ncmBeta", Arrays.asList(0.0, 0.25, 0.5));

// GridSearch can either be called with a ChemPredictor 
ChemCPClassifier chemACP = ...;  // Init and load data
GridSearchResult res = gs.search(chemACP, new ObservedFuzziness());

// or using a Predictor and Dataset
ACPClassification acp = ...; // Init with NCM, ML algorithm and sampling strategy
Dataset data = ...; // Load data
GridSearchResult res = gs.search(data, acp, new ObservedFuzziness());
```

Further note that you can register callbacks using the `GridSearch.Builder.register(..)` with either a `ProgressCallback` or `ProgressMonitor` instance to e.g. format the progress output in a custom way or make it possible to abort execution due to time restraints. 

(api-encryption)=

## Encrypting models and data sets using the API

Encrypting and decrypting models can optionally be performed when saving and loading models, to secure the serialized data. The open source repo only supply an interface for this purpose (`EncryptionSpecification` supplied in the encrypt-api module), contact [Aros Bio](mailto:ola.spjuth@arosbio.com) for purchasing this extension. To save data/models in encrypted format a concrete and initialized EncryptionSpecification instance must be supplied to one of the `ModelSerializer.saveXX(..)` methods or the Conf-AI equivalent (`ConfAISerializer.saveModel(..)`).


