

(predictors)=

# Predictors

The predictors currently implemented in CPSign are: Aggregated Conformal Predictors (ACP), Transductive Conformal Predictors (TCP)
and Aggregated Venn-ABERS Predictors (CVAP) ({ref}`[4,5,7,8] <refs>`). They all rely on an underlying {ref}`scoring algorithm <ml_alg>`.

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## File format

CPSign stores numerical data in LibSVM/LibLinear format which is in the form:

```bash
<label> <index>:<value> <index>:<value> ..
<label> <index>:<value> <index>:<value> ..
..
```

Also note that the `<index>` **must start at 1** and not 0, to conform with LibLinear and LibSVM requirements.

## Problem class

The underlying data structure for numerical problems is the {code}`Problem` class that is accessible through the API. It provides means of manipulating data directly,
without having to write data to file. Here's some examples of what you can do:

```java
// Problem-class can be accessed without instantiating the CPSignFactory
Problem data = Problem.fromSparseFile(InputStream);

// Add data from another file:
// (requires that all indexing is the same of course!)
data.readDataFromStream(InputStream);

// Shuffle the records
data.shuffle();

// Clone a dataset to make a deep copy
Problem dataClone = data.clone();

// Splitting can be done with random shuffle
Problem[] problems = data.splitRandom(0.2); // 20% in first Problem, 80% in second one

// Or splitting can be done statically (keep ordering)
Problem[] problemsStaticSplit = data.splitStatic(100); // Split so first 100 records in first Problem
Problem[] problemsStaticSplitFraction = data.splitStatic(0.3); // 30% in first, 70% in second

// Write manipulated Problems to file (data now has combined two data files and random shuffled the records)
data.writeProblemToStream(outputStream, true); // chose to compress or not

// If you wish to encrypt the Problem, instantiate CPSignFactory with a license that
// supports encryption and get the encryption specification to store/load a Problem
// in encrypted format
EncryptionSpecification spec = encryptionFactory.getEncryptionSpec();
data.writeProblemToEncryptedStream(new FileOutputStream(encryptedFile), spec);
// Load the Problem back
Problem fromEncryptedFile = Problem.fromSparseFile(new FileInputStream(encryptedFile), spec);
```

It is also possible to *rescale* the features in a Problem. This seems to have very little impact on predictive performance when using signatures descriptors
as long as you start with a signatures height of at least 1 (using height 0 typically leads to larger feature numbers for individual atoms like Carbon, when rescaling
of features can have a positive impact on performance). Rescaling is done by calling the {code}`problem.rescale()` and rescaling of new examples is done internally and
saved within the model so the user doesn't need to do any manual work after rescaling is performed. Scaling is done with a normal
[min-max normalization](<https://en.wikipedia.org/wiki/Feature_scaling#Rescaling_(min-max_normalization)>) to \[0,1\].

(tcp)=

## Transductive Conformal Prediction

TCP can be instantiated and used in the following way:

```java
// Either directly by the classes themselves:
TCPClassification predictor = new TCPClassification(new LibLinear(LibLinearParameters.defaultClassification()));

// Or by CPSignFactory
TCPClassification predictor = factory.createTCPClassification(factory.createLibLinearClassification());

// Train the predictor (using the Problem with training data already loaded!)
predictor.train(data);

// For a new example - do a prediction
List<SparseFeature> example = ..;
Map<Integer, Double> pvals = predictor.predict(example);

// can also compute gradient - this is mostly useful for data derived form Signatures Descriptors
predictor.calculateGradient(example);

// Saving the predictor (not very useful in TCP as training is done at prediction-time, not batch before as in ACP)
ModelCreator.generateTrainedModel(predictor, new ModelInfo("TCP predictor"), new File("predictor.jar"), null);
```

Note that the {code}`predictor.train(data)` only sets the current training data set so it is available for the predictor to use,
each prediction requires the underlying scoring algorithm to be trained. This means that it is not as much gain in run time when saving
a TCP predictor, the only thing saved is the training data and the parameters of the predictor. Also note that
this also means that predictions are very computationally demanding for a TCP predictor as the scoring algorithm needs re-training for each prediction.
TCP is mostly intended for smaller problems, where no data can be left out for *calibrating* predictions (as used in ACP and CVAP).
Also note that LibLinear is **a lot** faster to train compared to LibSVM, so chose {ref}`scoring algorithm <ml_alg>` according
to restraints in runtime and quality of the predictions.

(acp)=

## Aggregated Conformal Prediction

Aggregated Conformal Prediction (ACP) and Cross-Conformal Prediction (CCP) are aggregations of multiple Inductive Conformal Predictors (ICPs).
The ACP and CCP only differs in how training data is partitioned, whereas CCP has a strict division so that each sample is part of the
*calibration set* only once, what is called **Folded Sampling**, and ACP is a random sampling of *calibration set* for each ICP, called **Random Sampling**.
Here is the normal usage of the ACP/CCP Predictor (Classification):

```java
// Chose your sampling-strategy Folded = CCP, Random = ACP, stratified possible (only for classification)
SamplingStrategy strategy = new RandomSampling(nrModels, calibrationRatio);
new RandomStratifiedSampling(nrModels, calibrationRatio);
new FoldedSampling(nrFolds);
new FoldedStratifiedSampling(nrFolds);

// Instantiate using constructors
ACPClassification predictor = new ACPClassification(new LibSvm(LibSvmParameters.defaultClassification()), strategy);

// Or using CPSignFactory
ACPClassification predictor = factory.createACPClassification(factory.createLibLinearClassification(), strategy);

// Train the predictor (using the Problem with training data already loaded!)
predictor.train(data);

// For a new example - do a prediction
List<SparseFeature> example = ..;
Map<Integer, Double> pvals = predictor.predict(example);

// can also compute gradient - this is mostly useful for data derived form Signatures Descriptors
predictor.calculateGradient(example);

// Save predictor
predictor.setModelInfo(new ModelInfo("ACP Classification")); // Minimum info is to set the model name
predictor.save(new File("predictor.jar"));

// Load a previously saved predictor
ACPClassification loadedPredictor = (ACPClassification) ModelLoader.loadModel(new File("predictor.jar"), null);
```

Same example but for regression:

```java
// Chose your sampling-strategy Folded = CCP, Random = ACP, stratified *NOT* possible
SamplingStrategy strategy = new RandomSampling(nrModels, calibrationRatio);
new FoldedSampling(nrFolds);

// Instantiate using constructors
ACPRegression predictor = new ACPRegression(new LibLinear(LibLinearParameters.defaultRegression()), strategy);

// Or using CPSignFactory
ACPRegression predictor = factory.createACPRegression(factory.createLibLinearRegression(), strategy);

// Train the predictor (using the Problem with training data already loaded!)
predictor.train(data);

// For a new example - do a prediction, either using single or multiple confidences
List<SparseFeature> example = ..;
CPRegressionResult res = predictor.predict(example, confidence);
List<CPRegressionResult> results = predictor.
     predict(example, Array.asList(conf1, conf2, conf3));
// Predictions can also be performed using a distance to the predicted midpoint
List<CPRegressionResult> distanceResults = predictor.
     predictDistances(example, Arrays.asList(0.5, 1.5))

// can also compute gradient - this is mostly useful for data derived form Signatures Descriptors
predictor.calculateGradient(example);

// Save predictor
predictor.setModelInfo(new ModelInfo("ACP Regression")); // Minimum info is to set the model name
predictor.save(new File("predictor.jar"));

// Load a previously saved predictor
ACPRegression loadedPredictor = (ACPRegression) ModelLoader.loadModel(new File("predictor.jar"), null);
```

The regression case differs a bit compared to classification in that a confidence or distance is required to set at predict-time. Thus the result is a {code}`CPRegressionResult` giving many different values:
: - {code}`getY_hat()` : The median midpoint from all ICPs
  - {code}`getConfidence()` : Either given confidence to {code}`predict(conf)` or calculated confidence from {code}`predictDistance(distance)`
  - {code}`getDistance()` : Either given distance to {code}`predictDistance(distance)` or calculated distance from {code}`predict(conf)` (i.e. distance from midpoint to either of the sides of the interval).
  - {code}`getE_hat()` : Predicted error from the error-model (in case an error model is used)
  - {code}`getInterval()` : The calculated interval: (*ŷ-distance*, *ŷ+distance*)
  - {code}`getCappedInterval()` : The same as above interval, but capped to min and max values found in training data
  - {code}`getMaxObs()` : Maximum observed value found in training data (used for capping output interval)
  - {code}`getMinObs()` : Minimum observed value found in training data (used for capping output interval)

(cvap)=

## Venn-ABERS Prediction

VAP is new to CPSign v0.7.0, usage is similar to ACP classification, allowing both for a
folded (Cross Venn-ABERS) or random (Aggregated Venn-ABERS) prediction:

```java
// Chose your sampling-strategy Folded or Random, stratified possible
SamplingStrategy strategy = new RandomSampling(nrModels, calibrationRatio);
new RandomStratifiedSampling(nrModels, calibrationRatio);
new FoldedSampling(nrFolds);
new FoldedStratifiedSampling(nrFolds);

// Instantiate using constructors
AVAPClassification predictor = new AVAPClassification(new LibLinear(LibLinearParameters.defaultClassification()), strategy);

// Or using CPSignFactory
AVAPClassification predictor = factory.createVAPClassification(factory.createLibLinearClassification(), strategy);

// Train the predictor (using the Problem with training data already loaded!)
predictor.train(data);

// For a new example - do a prediction
List<SparseFeature> example = ..;
AVAPClassificationResult result = predictor.predict(example)

// can also compute gradient - this is mostly useful for data derived form Signatures Descriptors
predictor.calculateGradient(example);

// Save predictor
predictor.setModelInfo(new ModelInfo("CVAP Classification")); // Minimum info is to set the model name
predictor.save(new File("predictor.jar"));

// Load a previously saved predictor
AVAPClassification loadedPredictor = (AVAPClassification) ModelLoader.loadModel(new File("predictor.jar"), null);
```

The result from the predict method is a {code}`AVAPClassificationResult` which has several values:
: - {code}`getProbabilities()` : The probability for each value, this is the most useful value!
  - {code}`getMeanIntervalWidth()` : The mean with between the predictions for p0 and p1, **lower value -> more certain prediction**
  - {code}`getMedianIntervalWidth()` : The median with between the predictions for p0 and p1, **lower value -> more certain prediction**
  - {code}`getIntervals()` : A map with the predicted p0-p1 intervals for each Inductive Venn-ABERS predictor, not interesting for most people
  - {code}`getP0s()` : Not interesting for most people
  - {code}`getP1s()` : Not interesting for most people
