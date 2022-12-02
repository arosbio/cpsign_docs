

# Conformal Prediction Background

Conformal Prediction is described in greater depth in [[4-5, 10]](refs), but here is the short introduction of the basics and the specifics of how CPSign names and computes certain things. 

```{contents} Table of Contents
:depth: 2
```

## Conformal Prediction light introduction

Conformal prediction is a well established mathematical framework that delivers object-based predictions, an alternative approach
to the domain applicability estimation. The main advantages with conformal prediction is:

- Object-based prediction, thus giving a larger **prediction interval** (regression) to harder examples and a narrower one for easier examples), assuming an error model is used.
- Allows the user to set a desired **confidence**, the framework will then guarantee that the predictions will be at least of that confidence.
  Instead of estimating the accuracy of the model and then not being able to change that in any way.
- Classification can be done in a **mondrian** fashion, meaning that the predictions are calibrated independently for each class, solving problems with uneven class-distributions. **Note**: CPSign only supports mondrian calibration.

Conformal Prediction is proven to be well calibrated for Inductive Conformal Prediction (ICP) and Transductive Conformal Prediction (TCP),
where the former is far superior in computational costs and the latter in predictive performance. The TCP comes with rather extreme computational
costs, as each new prediction requires the underlying scoring model to be re-trained/fitted for each new prediction to be made, thus only feasible
for smaller datasets. ICP instead only trains a single scoring model (and possibly an error model in the case of regression), but sacrifice
data to a **calibration set** which is not used for training the scoring model (examples used for training the scoring model is called the **proper training set**).

To improve the predictive performance of ICPs, the Aggregated Conformal Predictor (ACP) and Cross-Conformal Predictor (CCP) methods were developed, or their more recent name Split Conformal Predictors. These methods simply combine several ICPs were the calibration set and proper training sets are picked either at random (ACP) or in a folded fashion (CCP). The
ACP and CCP methods has proven to improve the predictive performance of the ICP, but is not mathematically proven to give well calibrated predictions.
There is thus a tradeoff between performance and mathematical guarantees.

## Conformal Predictor Validity

As stated in the section above, the ICP and TCP methods are proven to give well calibrated predictions under the randomness assumption. However, we expect certain deviations from "perfect calibration" due to finite test set size and if training- and test-sets have been generated in a non-random way. To get an idea of the validity of the predictor, especially for the ACP and CCP methods that are not proven well calibrated, we look at the *calibration plot* - relating required confidence to predictor accuracy.

### Accuracy computed in CPSign

In the **regression** case, CPSign computes the accuracy of a model as the proportion of examples where the true (observed value) is in the prediction interval given by the predictor. Accuracy will thus be in the range `[0,1]` where 0 means that no examples were correctly predicted and 1 means that all of them were predicted correctly.

In the **classification** case, CPSign computes accuracy of a model as proportion of examples where the predictor has given a prediction set which
includes the true label (observed label). This is done regardlessly if the predicted region only contains a single label or multiple labels.

## Conformal Predictor Efficiency

The efficiency of a Conformal Predictor can be measured in several ways and CPSign include some of the most commonly used.

Classification metrics:

- Proportion multi-label predictions (minimization, confidence dependent)
- Proportion single-label prediction (maximization, confidence dependent)
- Observed Fuzziness (minimization, confidence independent)

For regression, there is only the median or mean prediction interval width implemented. This metric is confidence dependent and
something that should be minimized.

(cpgradient)=

## Conformal Prediction Gradient

In CPSign we have implemented a way to compute the *gradient* for the prediction of an example. Here we aim to clearify how this
is done. Here is how the calculation is performed for each Inductive Conformal Predictor or Transductive conformal Predictor.

1. We do a normal prediction with the example as it is.

   Regression

   : Normal prediction give a predicted value *ŷ*{sub}`N`

   Classification

   : Normal prediction give a p-value for each class, the largest p-value is tanken as the *selected class* and
     only the p-value for this class is regarded from this point. We can call this *pval*{sub}`N`.

2. For each present feature *f* in the example we increase *f* by an amount called *stepsize\**. For each *altered example*
   we make a new prediction. For an example with *N* non-zero features, we make *N* of these predictions, each with only one
   feature being altered.

3. Regression
   : For each of the *N* altered predictions, we get a predicted *ŷ*{sub}`A(i)` {i = 1,2,..*N*} value that *might* differ compared to the non-altered prediction
     in step 1. The difference in predicted values (*ŷ*{sub}`A(i)` - *ŷ*{sub}`N`)/*stepsize* can then be interpreted as the gradient for feature *i*.

   Classification
   : For each of the *N* altered predictions, we get the p-values for the *selected class*: *pval*{sub}`A(i)` {i = 1,2,..*N*}. This p-value
     *might* differ compared to the non-altered p-value *pval*{sub}`N`. The difference (*pval*{sub}`A(i)` - *pval*{sub}`N`)/*stepsize* can then be interpreted
     as the gradient for feature *i*.

\* *stepsize* can be changed by setting the value in IACPClassificationImpl, IACPRegressionImpl, ICCPClassificationImpl or ICCPRegressionImpl respectively.

**Note 1 (classification)** If the gradient value for feature *i* is **positive** the altered prediction have given a larger *pval*{sub}`A(i)` than *pval*{sub}`N` meaning that
adding more of this feature would move the prediction into being **more likely** to be of the selected class. Still note that we do only compute the gradient for the class that
has the highest pvalue to start with.

**Note 1 (regression)** If the gradient value for feature *i* is **positive** the altered prediction have given a larger regression value, meaning that adding more of this feature
would move the prediction into a higher response value, and vice-versa if the gradient value is negative.

**Note 2** The gradients will not be normalized at this level, classification gradient values can be within \[-1,1\] and regression values can potentially be \[-∞,∞\].
On the Signatures level we can provide normalized gradients, see {ref}`molecule-gradient`.

### Aggregated Conformal Predictor- and Cross-conformal Predictor gradients

Aggregated Conformal Predictors (ACPs) and Cross-conformal Predictors (CCPs) uses several Inductive Conformal Predictors (ICPs), each which will produce their own gradient.
It is fully possible that the gradients contradict each other. In CPSign we typically use the median value produced by the ICPs so that individual ICPs does not get too much
influence on the results. When computing the gradient of the prediction, we use the median value per *each feature*.

(molecule-gradient)=

### Molecule Gradient

At the *Signatures level* we can infer further knowledge from the raw gradients produced in {ref}`cpGradient`. If a Signatures Problem is predicted, each feature
is in reality mapped to one or multiple atoms in a molecule. At the signatures level, we convert the *signature based* gradients into *atom based* gradients. This is done by
the following steps:

1. Each atom is initially set to have a gradient value of 0.
2. For every index in the *signature based* gradient (*g*{sub}`S(i)`), get the set of atoms *A*{sub}`i` that are part of that signature. For every atom in *A*{sub}`i`, add the
   gradient value of *g*{sub}`S(i)` to current atom-gradient.

The molecule gradient will have the total contribution of an atom (from all the signatures that it's part of) and each atom-gradient can have any real value.

**Note** that classification gradients will no longer have any restrictions in their range.

#### Normalization of gradients

The molecule gradients will theoretically take any value on the real axis for each atom. The gradient values will also be model- and dataset-dependent, we need some sort of normalization
to be able to gain more information. CPSign handles this in the CLI by predicting the gradient for a large set of molecules from the training set. All gradient values will then be ordered
and the values from the lowest 10 % (lowPercentile) and the highest 10 % (highPercentile) will then be tanken as a lower and upper limit of the gradient values.
This range \[lowPercentile, highPercentile\] will then be used to linearly normalize the results to a range of \[-1, 1\], the positive and negative values will be handled separately (each with their own linear normalization).
Once normalized, it's a lot easier to interpret the results, values close to 0 has low influence on the prediction, values close to +1 has positive significant influence and values close to -1
has negative significant influence.

**Note** If you are running CPSign with API and wish to get normalized gradients, you have to call the {code}`computePercentiles` method after you've trained your models. This has to be done with
a "large enough" set of molecules, otherwise the lower and upper values might be misleading and affect the normalization. If you've not computed lower and upper values, the molecule gradients will
be given but non-normalized and a logger.warning message will be printed.

(signsign)=

### Significant Signature

The Significant Signature of a molecule is simply the signature that produced the largest absolute gradient value in the *signature based* gradient. It is then easy to get the mapping of the signature
to which atoms that the signature belongs to.

Classification

: The significant signature atom mapping will be a map between atom to which class that has the highest p-value.

Regression

: The significant signature atom mapping will be a map between atom to 1 (simply indicating that the atom is part of the significant signature).

The Significant Siganture atom mapping can be used in image rendering (thus coloring atoms after either the class having the hightest p-value or simply coloring the atoms that belong to the significant signature).

(nonconf-measure)=

## Nonconformity measures

A central concept of Conformal Prediction is the nonconformity measure, which is simply a way to compute how different an example is compared to the other examples in the dataset (see {ref}`[4-5] <refs>` for a thorough explanation).
Here we simply state that there are different ways to compute the nonconformity of an example, both in the regression and classification cases we support three different measures each, described below. Furthermore, in the API it is possible
to add your own *custom* measure.

**Definitions** The nonconformity value of example *i* is generally denoted as *α*{sub}`i`. In the regression case it is common to train and
use an *error model* generally denoted as *e* or *μ*. The *predicted error* for an example *i* is denoted *ê*{sub}`i`. Moreover, the true label of an example *i* is
denoted *y*{sub}`i` and the predicted label for the same example is denoted *ŷ*{sub}`i`.

### Absolute difference measure (Regression)

When using the prediction error as the nonconformity score for an example *i*, we do not need to train any error model. The nonconformity of example *i*, denoted
*α*{sub}`i` is calculated as:

*α*{sub}`i` = | *y*{sub}`i` - *ŷ*{sub}`i` |

It is worth noting that by using this nonconformity measure, we do not need to train any error model, thus roughly preformning the training in half the runtime of any
nonconformity measure that requires an error model.

### Normalized measure (Regression)

By using a *normalized* nonconformity measure, one hopes to aquire a better prediction result by also training an *error model* that will increase the interval size
for "difficult examples" and decrese the interval size for "easy examples". The nonconformity is calculated as follows:

*α*{sub}`i` = | *y*{sub}`i` - *ŷ*{sub}`i` | / *ê*{sub}`i`

The error model is then trained on the absoute error for each example, | *y*{sub}`i` - *ŷ*{sub}`i` | .

### Logarithmically normalized measure (Regression)

A logarithmic normalization, proposed in {ref}`[7] <refs>` instead uses the logarithm of the predicted error in the error model. It also introduces a smoothing factor, β, that should
is used for "smoothing" the interval sizes, making the small intervals a bit larger and the very large intervals a bit smaller. The smoothing factor, β,
must itself be chosen, as found in \[7\], already a β of 0.5 was too big. Optimization of β can be done with the {ref}`tune <tune>` command.

*α*{sub}`i` = | *y*{sub}`i` - *ŷ*{sub}`i` | / (exp(*ê*{sub}`i`) + β), β>=0

The error model is then trained on the natural logarithm of the absolute error, ln(| *y*{sub}`i` - *ŷ*{sub}`i` |).

### Negative distance to SVM hyperplane (Classification)

Use the negative distance to the decision plane, i.e. instances on the "wrong side" of the decision plane is considered
the most non-conforming, whereas the farther on the correct side of the plane, the more conforming is the example.

### Positive distance to SVM hyperplane (Classification)

Use the positive distance, i.e. instances farther from the decision plane is considered more non-conforming, though examples
ending up in the "wrong side" of the decision plane is then considered the *most conforming* as they get a negative value.
This NCM is abundant in literature and thus included, but has not worked well in our own tests.

### Probability estimates (Classification)

Use probability estimates (from e.g. libsvm) that are not true, well calibrated, probabilities. Calibrate these estimates into
well-calibrated p-values using CP. *NOTE: this NCM allows multi-class classification* - but the probability estimates are even
more computationally demanding than the plain LibSVM and can thus be very slow to train.
