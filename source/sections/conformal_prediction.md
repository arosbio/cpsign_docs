

# Conformal Prediction Background

Conformal Prediction is described in greater depth in [[4-5, 10]](references.md), but here is the short introduction of the basics and the specifics of how CPSign names and computes certain things. 

```{contents} Table of Contents
:depth: 2
```

## Conformal Prediction light introduction

Conformal prediction is a well established mathematical framework that delivers object-based predictions. The main advantages with conformal prediction are:

- Object-based predictions, thus giving larger **prediction intervals** (regression) to more difficult objects and a narrower one for easier objects, assuming an error/normalization model is used. For classification the **prediction sets** gets wider for more difficult predictions, and vice versa.
- Allows the user to set a desired **confidence** level, the framework will then guarantee that the predictions will be at least of that confidence. This is achieved by generating either tighter or wider prediction sets. 
- Classification can be done in a **mondrian** fashion, meaning that the predictions are calibrated independently for each class, solving problems with uneven class-distributions. **Note**: CPSign only supports mondrian calibration.

These advantages should be compared to traditional supervised machine learning were the user has to rely on overall (non-object based) performance metrics estimated on withheld data, or other methods for generating uncertainty estimates - but which often comes with higher computational overhead, requirements on data distributions or with less guarantees in terms of calibration. 


Conformal Prediction is proven to be well calibrated for Inductive Conformal Prediction (ICP) and Transductive Conformal Prediction (TCP), where the former is far superior in computational costs and the latter (often) in predictive performance. The TCP comes with rather extreme computational costs, as each new prediction requires the underlying scoring model to be re-trained/fitted for each new prediction to be made, thus only feasible for smaller datasets. ICP instead only trains a single scoring model (and possibly an error model in the case of regression), but sacrifice data to a **calibration set** which is not used for training the scoring model (examples used for training the scoring model is called the **proper training set**).

To improve the predictive performance of ICPs, the Aggregated Conformal Predictor (ACP) and Cross-Conformal Predictor (CCP) methods were developed, or their more recent name Split Conformal Predictors. These methods simply combine several ICPs were the calibration set and proper training sets are picked either at random (ACP) or in a folded fashion (CCP). The ACP and CCP methods has proven to improve the predictive performance of the ICP, but is not mathematically proven to give well calibrated predictions. There is thus a tradeoff between performance and mathematical guarantees. Calibration can still be empirically assessed. 

## Conformal Predictor Validity

As stated in the section above, the ICP and TCP methods are proven to give well calibrated predictions under the exchangeability assumption (largely similar to the **IID** assumption that is widely used in ML). However, we expect certain deviations from "perfect calibration" due to finite test set size and if training- and test-sets have been generated in a non-random way. To get an idea of the calibration of the predictor, especially for the ACP and CCP methods that are not proven well calibrated, we look at the *calibration plot* - relating required confidence to predictor accuracy.

### Accuracy computed in CPSign

In the **regression** case, CPSign computes the accuracy of a model as the proportion of examples where the true (observed value) is within the prediction interval given by the predictor. Accuracy will thus be in the range `[0,1]` where 0 means that no examples were correctly predicted and 1 means that all of them were predicted correctly.

In the **classification** case, CPSign computes accuracy of a model as proportion of examples where the predictor has given a prediction set which includes the true label (observed label). This is computed regardlessly if the predicted region only contains a single label or multiple labels.

## Conformal Predictor Efficiency

The efficiency of a Conformal Predictor can be measured in several ways and CPSign include some of the most commonly used. Conformal models are sometimes evaluated using *forced predictions*, meaning that the predictions sets are turned into point predictions - these can then be evaluated using standard ML metrics. These forced predictions are calculated by predicting the class with the largest p-value and the midpoint of the prediction interval for a regressor model. Here follows the currently available metrics and brief information such as they should be minimized (otherwise they should be maximized), if they depend on confidence level, or if they are based on forced predictions. Apart from these there is also a metric for conformal accuracy, but that is excluded from this overview as it is a way to measure *calibration* rather than *efficiency*.

### Classification metrics:


|Short name | Minimize | Confidence-dependent | Based on forced predictions | Description |
|---|:-:|:-:|:-:| --|
|PropMultiLabel | x| x|| Proportion multi-label predictions |
|PropSingleLabel| | x|| Proportion single-label predictions |
|OF| x| || Observed Fuzziness |
|BalancedOF| x| || Observed Fuzziness, weighting the classes equally |
|AvgC | x | || Average number of classes in prediction sets |
|CP_Confidence | | | | Unobserved metric. Calculates the average of `1 - 'second largest p-value'` in the predictions|
|CP_Credibility | | | | Unobserved metric. Calculates the average of the largest p-value in the predictions|
||||||
|Classifier Accuracy | | | x |Calculates the percentage of accurate predictions out of all predictions|
|Balanced Accuracy | | | x | Computes the accuracy of a standard classifier, balanced so that each class has the same impact on the score |
|ROC_AUC |  | |x|  The area under curve (AUC) of the receiver operating characteristic (ROC)|
|F1Score | | |x| F1 score, when used for optimization the Macro F1 score is used, but calculates both the micro and the weighted F1 score as well. |
|Precision |  | |x| Precision or Positive Predictive Value (PPV) |
|Recall |  | |x| Recall, Sensitivity or True Positive Rate (TPR) |
|NPV |  | |x| Negative Predictive Value |


### Regression metrics:
|Short name | Minimize | Confidence-dependent | Width-dependent | Based on forced predictions | Description |
|---|:-:|:-:|:-:|:-:| --|
|MeanPredWidth | x| x||| Mean prediction interval width |
|MedianPredWidth | x| x||| Median prediction interval width |
|ConfGivenIntervalWidth| | |x|| Calculates the confidence for a given prediction interval width |
||||||
|MAE |x| | | x |Mean Absolute Error|
|R^2 || | | x | The coefficient of determination |
|RMSE ||  | |x|  The area under curve (AUC) of the receiver operating characteristic (ROC)|


(conformal-gradient)=
## Conformal Gradient

The contributions to the predictions can be visualized based in the *gradient* calculation described in Ahlberg et al. [[5]](references.md), extended for usage in regression and Venn-ABERS based models. Here is the simplified algorithm for how this is calculated, depending on the predictor type being used.

1. Perform a normal prediction based on the original (unaltered) object, saving the result in $\hat{y}_{orig}$. Depending on the model that is used, the $\hat{y}_{orig}$ is defined as;

   **Conformal regressor**: The midpoint of the prediction (i.e., simply what the scoring model outputs - ignoring the generated interval).

   **Conformal classifier**: Picking the largest p-value and selecting the class which this p-value corresponds to.

   **Venn-ABERS classifier**: Picking the largest probability and selecting the class which this probability corresponds to.

2. For each non-zero feature *f* in the object we increase *f* by an amount called *stepsize\** - creating altered objects $x_i$, for $i$={0,1,..,*N*} for the original feature vector *x* having *N+1* non-zero features. For each *altered object* we make a new prediction - resulting in $\hat{y}_i$ (as defined in the step above, for classification using the same selected class from the original prediction). The gradient for index *i* is then calculated as the normalized change in the prediction according to; ($\hat{y}_i - \hat{y}_{orig}$)/*stepsize*.

\* The *stepsize* parameter can be altered when using the Java API, having the default value of 1.0.


**Note 1 (classification)** If the gradient value for feature *i* is **positive** the altered prediction resulted in a larger *p-value*/*probability* compared to the unaltered object, meaning that adding more of this feature leads to the object being **more likely** to be part of the *selected class*. Further note this only considers the selected class and ignores the change in prediction for the other p-values or probabilities.

**Note 2 (regression)** If the gradient value for feature *i* is **positive** the altered object have given a larger regression value, meaning that adding more of this feature would move the prediction into a higher response value, and vice versa if the gradient value is negative.

**Note 3** The gradients are **not normalized** at this level, classification gradient values can be within \[-1,1\] and regression values can potentially be \[-∞,∞\]. On the Signatures level we can provide normalized gradients, see [Molecule gradient](molecule-gradient).

### Aggregated Conformal Predictor- and Cross-conformal Predictor gradients

Aggregated Conformal Predictors (ACPs) and Cross-conformal Predictors (CCPs) uses several Inductive Conformal Predictors (ICPs), each which will produce their own gradient. It is fully possible that the gradients contradict each other. In CPSign we use the median value produced by the ICPs so that individual ICPs does not get too much influence on the results. When computing the gradient of the prediction, we use the median value for *each feature*.

(molecule-gradient)=
### Molecule Gradient

At the *Signatures level* we can infer further information from the raw gradients produced in [Conformal gradient](conformal-gradient), as each signature can be mapped back to one or several atoms that it stem from. At the signatures level, we convert the *signature based* gradients into *atom based* gradients. This is done by the following steps:


1. Input: the raw gradient, $g_{orig}$, of length *N+1* from last section. Initialize the molecule gradient $g_{mol}$ of length equal to the number of non-hydrogen atoms, with 0 for all indices (i.e. one index for each atom). 
2. For every index, $i$, in $g_{orig}$ ($i$={0,1,..,*N*} from above), find the atom(s) the feature/signature stem from and add the gradient value ($g_{orig,i}$) to all indices in $g_{mol}$ that correspond to those atoms.

The resulting molecular gradient will have the total contributions, mapped to atoms individual contributions to the prediction. Each index can have any real value (although with high probability from a rather small range).

**Note** that classification gradients will no longer have any restrictions in their range.

#### Normalization of gradients

The molecule gradients will theoretically take any value on the real axis for each atom. The gradient values will also be model- and dataset-dependent, thus needing normalization in order to be interpretable. In CPSign we require the user to compute *percentiles* (in the CLI using the parameter `--percentiles-data` and `ChemPredictor.computePercentiles(..)` in the API) in order to get a range to normalize the gradient based on. 

**Note** If you are running CPSign using the API and wish to get normalized gradients, you have to call the {code}`computePercentiles` method after you've trained your models. This has to be done with a "large enough" set of molecules, otherwise the lower and upper values might be misleading and affect the normalization. If you've not computed lower and upper values, the molecule gradients will be given but non-normalized and a logger.warning message will be printed.

### Significant Signature

The Significant Signature of a molecule is simply the signature that produced the largest absolute gradient value in the *signature based* gradient. That atoms part of the signature can then be highlighted in depictions.


## Nonconformity measures

A central concept of Conformal Prediction is the nonconformity measure, which is simply a way to compute how different an observation is compared to the other observations in the dataset (see [[4-5]](references.md) for a thorough explanation).
Here we simply state that there are different ways to compute the nonconformity of an observation, both in the regression and classification cases we support four different measures each, described below. Furthermore, it is one of the possible extendable features, see [Customizations](customization.md).

**Definitions** The nonconformity value of observation $i$ is generally denoted as $\alpha_i$. In the regression case it is common to train and use an *error model* generally denoted as $e$ or $\mu$. The *predicted error* for an object $i$ is denoted $\hat{e}_i$. Moreover, the true label of an example $i$ is denoted $y_i$ predicted label for the same example is denoted $\hat{y}_i$. For the regression metrics it is common to use a smoothing term called denoted $\beta$ (requiring $\beta\geq0$), both for numerical reasons and to make sure that prediction intervals do not get overly tight or wide. Further note that all definitions relies implicitly on the object ($x_i$), i.e. nonconformity is more correctly defined as $\alpha(x_i)$, but simplified here as $\alpha_i$ to note that it is the value for object/observation $i$ (but relying on the object $x_i$).

### Regression NCMs
|Name | Formula | Error model |Description |
|:-|:-:|:-:|-|
|AbsDiff|$\alpha_i = \| y_i - \hat{y}_i\|$ | None | Only calculates prediction intervals based on the calibration data, no normalization based on the predicted difficulty of the predicted object. | 
|Normalized | $\alpha_i = \frac{\| y_i - \hat{y}_i\|}{\hat{e}_i + \beta}$ | $r = \|y_i - \hat{y}_i \|$ | Trains error model ($\hat{e}$) for normalization, replaces the label ($y$) with the absolute value of the residuals ($r$) in the error model.|
|LogNormalized | $\alpha_i = \frac{\|y_i - \hat{y}_i \|}{\exp{(\hat{e}_i)} + \beta}$ |$r=ln(\| y_i - \hat{y}_i \|)$ | Trains error model ($\hat{e}$) for normalization, replaces the label ($y$) with natural logarithm of the residuals ($r$) in the error model.  | 
|SignedNormalized | $\alpha_i = \frac{\| y_i - \hat{y}_i\|}{\|\hat{e}_i\| + \beta}$|$r = y_i - \hat{y}_i $  | Similar as the Normalized NCM, but moves the absolute value from the residuals of the error model to the NCM calculation. | 

Commonly for the normalized NCMs is that an error model is used for predicting the difficulty or error of the object in order to scale the prediction intervals accordingly. The error model is trained on the proper training set, but replaces the target values ($y$) with the label $r$ as defined in the table.


### Classification NCMs
|Name | Formula | Description |
|:-|:-:|-|
|NegativeDistanceToHyperplane|$\alpha_{i,L} = - d_{H,L}$ |Requires a SVC model as underlying scoring model, calculating the distance ($d$) from the hyperplane ($H$) with respect to the considered label ($L$). Objects ($x$) further into "$L$ side" of hyperplane are more conforming, i.e. more nonconformity using the negative distance. | 
|PositiveDistanceToHyperplane|$\alpha_{i,L} = d_{H,L}$ |Similar to the negative distance, but with swapped sign. This generally performs worse than the above definition, but literature loosely defines nonconformity as distance to hyperplane - which would translate to this equation.| 
|InverseProbability | $\alpha_{i,L} = 1 - p(L)$ |Requires a probabilistic scoring model, considering the inverse of the probability of the considered label ($L$).| 
|ProbabilityMargin |$\alpha_{i,L} = \frac{1 - p(L) + \max_{l \in S \setminus L} p(l)}{2}$ |Requires a probabilistic scoring model. Looks at the margin between the probability of the considered class ($L$) vs the highest probability of any other class from the set of available class labels ($S$). | 


