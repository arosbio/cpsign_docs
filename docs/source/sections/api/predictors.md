

(sparse-predictors)=

# Predictors

The predictors currently implemented in CPSign are: Aggregated Conformal Predictors (ACP and CCP), Transductive Conformal Predictors (TCP) and Aggregated Venn-ABERS Predictors (CVAP) ({ref}`[4,5,7,8] <refs>`). They all rely on an underlying {ref}`scoring algorithm <ml_alg>`.

```{contents} Table of Contents
:backlinks: top
:depth: 3
```



(tcp)=

## Transductive Conformal Prediction

TCP can be instantiated and used in the following way:

```java
// Define underlying scoring model - this case a SVC 
LinearSVC svc = new LinearSVC();
// Decide on nonconformity measure to use
// Here using the default one, requiring a SVC as scoring model
NCMMondrianClassification ncm = new NegativeDistanceToHyperplaneNCM(svc);
// Instantiate the CPClassifier instance
TCPClassifier predictor = new TCPClassifier(ncm);

// Load training data
Dataset data = ...

// Train the predictor
predictor.train(data);

// Predict a new test object
FeatureVector testObject = ..
Map<Integer, Double> pvals = predictor.predict(testObject);

// Saving the predictor - i.e. training data with all hyper-parameters
ConfAISerializer.saveModel(predictor, new File("predictor.jar"), null);
```

Note that the `predictor.train(data)` only sets the current training data so it is available for the predictor to use, each prediction requires the underlying scoring algorithm to be trained. This means that it is not as much gain in run time when saving a TCP predictor, the only thing saved is the training data and the parameters of the predictor. Also note that this also means that predictions are computationally demanding for a TCP predictor as the scoring algorithm needs re-training for each prediction. TCP is mostly intended for smaller problems, where no data can be left out for *calibrating* predictions (as used in ACP and CVAP). Also note that LibLinear is **a lot** faster to train compared to LIBSVM, so chose {ref}`scoring algorithm <ml_alg>` according to restraints in runtime and quality of the predictions.

(acp)=

## Aggregated Conformal Prediction

Aggregated Conformal Prediction (ACP) and Cross-Conformal Prediction (CCP) are aggregations of multiple Inductive Conformal Predictors (ICPs) or Split-Conformal Predictors. The ACP and CCP only differs in how training data is partitioned, where CCP has a strict division so that each sample is part of the *calibration set* only once, what is called **Folded Sampling**, and ACP uses a random sampling of *calibration set* for each ICP, called **Random Sampling**. Here is the normal usage of the ACP/CCP Predictor (Classification):

```java
// Chose your sampling-strategy Folded = CCP, Random = ACP, stratified possible (only for classification)
SamplingStrategy strategy = new RandomSampling(nrModels, calibrationRatio);
new RandomStratifiedSampling(nrModels, calibrationRatio);
new FoldedSampling(nrFolds);
new FoldedStratifiedSampling(nrFolds);

// Chose the underlying scoring algorithm
C_SVC svc = new C_SVC(); // RBF kernel SVC 

// Chose the nonconformity metric
NCMMondrianClassification ncm = new NegativeDistanceToHyperplaneNCM(svc);

// Instantiate predictor instance
ACPClassifier predictor = new ACPClassifier(ncm, strategy);

// load data & manipulate data
Dataset data = .. 

// Train the predictor 
predictor.train(data);

// Predict a new test object
FeatureVector testObject = ..;
Map<Integer, Double> pvals = predictor.predict(testObject);

// Save predictor (optionally set model info before)
predictor.setModelInfo(new ModelInfo("ACP for <some endpoint>"));
ConfAISerializer.saveModel(predictor, new File("predictor.jar"), null);

// Load a previously saved predictor
ACPClassifier loadedPredictor = (ACPClassifier) ConfAISerializer.loadPredictor(new File("predictor.jar"), null);
```

Same example but for regression:

```java
// Chose your sampling-strategy Folded = CCP, Random = ACP, stratified *NOT* possible
SamplingStrategy strategy = new RandomSampling(nrModels, calibrationRatio);
new FoldedSampling(nrFolds);

// Chose underlying scoring algorithm
SVR svr = new EpsilonSVR(); // RBF kernel SVR
// Chose error model (if NCM uses one)
SVR errorModel = new LinearSVR(); // e.g. linear kernel SVR, or use same as scorer

// Chose NCM to use
double beta = 0.01; // Smoothing parameter
NCMRegression ncm = new LogNormalizedNCM(svr,errorModel,beta);

// Instantiate predictor instance
ACPRegressor predictor = new ACPRegressor(ncm, strategy);

// Load data 
Dataset data = ..
// Train the predictor
predictor.train(data);

// Predict a new test object
FeatureVector testObject = ..
// Predict using single confidence level
CPRegressionPrediction res = predictor.predict(testObject, 0.8);

// Use several conf levels 
CPRegressionPrediction results = predictor.
     predict(example, Array.asList(conf1, conf2, conf3));

// Predictions can also be performed using a prediction interval width to get what level of confidence that would correspond to
CPRegressionPrediction distanceResults = predictor.
     predictConfidence(example, Arrays.asList(0.5, 1.5))

// Save the predictor (optionally set some meta info)
predictor.setModelInfo(new ModelInfo("ACP Regression"));
ConfAISerializer.saveModel(new File("predictor.jar"), null);

// Load a previously saved predictor
ACPRegressor loadedPredictor = (ACPRegressor) ConfAISerializer.loadPredictor(new File("predictor.jar"), null);
```

The output from the regression model differs compared to classification, as the confidence or distance is required to set at predict-time. Thus the result is a `CPRegressionResult` object containing several values:
  - `getY_hat()` : The median/mean midpoint from all ICPs
  - `getMinObs()` : Minimum observed value found in training data (used for capping prediction interval)
  - `getMaxObs()` : Maximum observed value found in training data (used for capping prediction interval)
  - `getIntervalScaling()` : The scaling of the prediction interval, i.e. the denominator from the nonconformity function - i.e. scaling depending on the difficulty (including potential smoothing). If no scaling should be applied, this should be 1.

These values are common for the prediction, and then one or several `PredictedInterval`s are given depending on confidence levels. These are fetched from or of the methods `getIntervals()`, `getWidthToConfidenceBasedIntervals()` or `getInterval(conf)`. Each `PredictedInterval` has the following getter methods:
- `getIntervalHalfWidth()`: Half of the interval width, such as the prediction interval is [$\hat{y}-hv$,$\hat{y}+hv$], where $hv$ stands for the interval half width.
- `getIntervalWidth()` : The full width of the interval, i.e. 2 times the above value
- `getInterval()` : A range object with the lower and upper endpoints
- `getCappedInterval()` : A range object with the lower and upper endpoints, but capped according to the `getMinObs()` and `getMaxObs()` values from the `CPRegressionResult` - so prediction intervals cannot span outside the observed label space.
- `getConfidence()` : The confidence level this interval corresponds to



(cvap)=

## Venn-ABERS Prediction
The Venn-ABERS predictor works in a similar fashion as the ACPClassifier and ACPRegressor described above, again the sampling strategy dictates if you use a Cross-Venn-ABERS (CVAP) or Aggregated Venn-ABERS (AVAP) predictor model. Instead of having to decide on a nonconformity metric as for the conformal models, the Venn-ABERS only take a scoring classifier algorithm and applies isotonic regression to yield the predictions. 

```java
// Chose your sampling-strategy Folded or Random, stratified possible
SamplingStrategy strategy = new RandomSampling(nrModels, calibrationRatio);
// or one of these:
new RandomStratifiedSampling(nrModels, calibrationRatio);
new FoldedSampling(nrFolds);
new FoldedStratifiedSampling(nrFolds);

// Scoring classifier to calibrate
LogisticRegression lr = new LogisticRegression();

// Instantiate predictor
AVAPClassifier predictor = new AVAPClassifier(lr, strategy);

// Load data 
Dataset data = ..

// Train the predictor 
predictor.train(data);

// Predict a new test object
FeatureVector testObject = ..
CVAPPrediction<Integer> result = predictor.predict(example)

// Save the predictor (optionally set some meta info)
predictor.setModelInfo(new ModelInfo("CVAP Classification")); 
ConfAISerializer.saveModel(new File("predictor.jar"), null);

// Load a previously saved predictor
AVAPClassifier loadedPredictor = (AVAPClassifier) ConfAISerializer.loadPredictor(new File("predictor.jar"), null);
```

The result from the predict method is a `CVAPPrediction` with a type argument of `<Integer>` simply meaning that the classes are of integer type. The reason for the type parameter is that the same class is returned from the ChemVAPClassifier but instead links textual labels to class probabilities. The instance stores all relevant information from the prediction:
  - `getProbabilities()` : The probability for each class label
  - `get[Mean|Median]P0P1Width()` : The mean or median width between the predictions for p0 and p1, **lower value -> more certain prediction**. Sometimes referred to as sharpness of the prediction. 
  - `getLabel0()` : The '0' label 
  - `getLabel1()` : The '1' label
  - `getP0List()` : Not interesting for most people
  - `getP1List()` : Not interesting for most people
