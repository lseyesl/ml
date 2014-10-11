ML
==

##Introduction

This package implement classic machine learning algorithms. Motivations for this project includes:

1. Help machine learning freshman to have a better and deeper understanding of the basic algorithms and model in this field.
2. Provide the `battery-included` code and data for ML prototyping.

Following algorithms and models will be implemented in this package. 
For some of them, `NumPy` support is necessary.
Ensure to configure `NumPy` with advanced linear algebra implementations(`BLAS`, `ATLAS`, `Lapack`). 
The default implementation is extremely slow.

1. Naive Bayes
2. Decision Tree
3. Perceptron
4. Linear Regression
5. Gaussian Discriminant Analysis
6. Logistic Regression
7. Softmax Regression
8. Support Vector Machines
9. AdaBoost
10. Matrix Factorization
11. Hidden Markov Models
12. Neural Network
13. Optimization

The following models and algorithms will be added into this package soon:

1. Sparse AutoEncoder
2. Deep Neural Network
3. L-BFGS
4. OWL-QN
5. Other unsupervised learning algorithms

##Naive Bayes

nb.py implements `Naive Bayes`(NB) classifier for both `Multi-variate Bernoulli` and `Multinomial` event model.

Run `python nb.py` to train and test NB on `20 Newsgroups` dataset.

By default, train and test data are generated by randomly split the benchmark dataset with an approximate ratio of `4:1`.

Accuracy for `Multi-variate Bernoulli` event model: `78.842676%`

Accuracy for `Multinomial` event model: `84.674503%`

NB is a generative model, different event model leads to different assumption on the distribution of p(x|y).

For example, in spam classification, 
after we decided to generate a spam or non-spam text according to the class prior p(y), 
we need to further decide which words to be included in the text, we can:

1. Go through the whole dictionary, for each word, toss a coin to decide whether to append the word to the text.
2. Randomly select words from the dictionary.

In the first case, each word is drawn from a Bernoulli distribution, 
and the text is generated by the `Multi-variate Bernoulli` event model.

In the second case, all words are drawn from a `Multinomial` distribution.

`TODO`: Parallel NB classifier.

##Decision Tree

dt.py implements `ID3` learning algorithm for training a decision tree(DT).

Run `python dt.py` to train and test ID3 on a subset of the `20 Newsgroups` dataset, 
which contains only 2 news groups: `comp.sys.ibm.pc.hardware.d` and `comp.sys.mac.hardware.e`.

Accuracy for `ID3` on this small dataset is `80.973451%`

`ID3` uses `infomation gain` as the split criteria, and `chi-square test` for early stoping.

It is easy to convert a DT to a rule-set. Check `data/dt.rule_set` for the rule-set learned by DT, like:

<pre><code>
IF (mac) THEN 
  IF (ide) THEN 
    PREDICT LABEL IS comp.sys.ibm.pc.hardware.d [branch-size : 8]
  ELSE 
    IF (controller) THEN 
      PREDICT LABEL IS comp.sys.mac.hardware.e [branch-size : 5]
    ELSE 
      IF (difference) THEN 
        IF (people) THEN 
          PREDICT LABEL IS comp.sys.ibm.pc.hardware.d [branch-size : 2]
        ELSE 
          PREDICT LABEL IS comp.sys.mac.hardware.e [branch-size : 13]
      ELSE 
        PREDICT LABEL IS comp.sys.mac.hardware.e [branch-size : 156]
</code></pre>

##Perceptron

perceptron.py implements `Perceptron Learning Algorithm`(PLA) and its variant `Pocket` for binary classification.

Run `python perceptron.py` to train and test PLA and `Pocket` on `heart-scale` dataset.

Accuracy for Perceptron: `75.471698%`

Accuracy for `Pocket` : `81.132075%`

PLA is one of the simplest machine learning algorithm: 
whenever a mistake happens, if the model misclassify a positive sample as negative, PLA adds the x to the w;
otherwise, substract it from w.

`Pocket` is the upgrade version of PLA: it always keep a copy of the best model after each update, 
and this is how the name `Pocket` come from.

PLA and `Pocket` are very old-fashioned ml technics, but they are very important, 
they are the foundations of Support Vector Machines and Neural Network, 
and the weighted version of `Pocket` will be used as the weak classifier of `AdaBoost`.

##Linear Regression

regression.py implements `Linear regression` model, in particular, `Ridge regression` for regression problem.

`Ridge regression` is one variant of the traditional regression model. 
By adding a regularization term to the loss function, the model increase its generalization ability, thus more robust to over-fitting.
From the statistics point of view, this transforms the original Maximum Likelihood Estimation(MLE) problem to the Maximum A Posterior(MAP) problem, and the L2 regularization term is equivalent to the `Gaussian prior distribution`. 

Another important variant of linear regression model, which is not implemented in this package yet, is `Lasso`.
Lasso differs from ridge regression in the regularization term, by using L1 norm instead of L2, the model being learned is `sparse`.
This property is very important in large-scale regression problem, and can be used as a way of doing feature selection.
But L1 norm is hard to optimized, so general optimization tools can't be used directly.
The sub-gradient methods can be helpful.

L1 norm is equivalent to the `Laplacian prior distribution`, 
and there are models that consider L1 and L2 norm simultaneously: the Elastic net model. 
For more details, please refer to Sam Roweis's lecture notes: http://www.cs.nyu.edu/~roweis/csc2515/

For optimization, simple gradient descent algorithm works fairly well. 
But for large-scale machine learning problems, the bottleneck lays in the computation of the loss function and gradient. 
In this situation, efficient optimization algorithm is necessary for reducing the total amount of iterations.

Here, the Conjugate Gradient methods is used in learning the regression model.
All the advanced optimization technics will be mentioned in Optimization section.

Type `python regression.py` to train and test Ridge regression model on the `housing prediction` problems.

RMSE on the test set : `5.125107`

`TODO`: Lasso/LARS, Elastic net

##Gaussian Discriminant Analysis

gda.py implements `Gaussian Discriminant Analysis`(GDA) model for binary classification.

GDA is a generative model, it makes the following assumptions:

1. p(y) is distributed according to a Bernoulli distribution.
2. Both p(x|y=0) and p(x|y=1) is distributed according to the multivariate Gaussian distributions.

Training a GDA model is simple, just do the Maximum Likelihood Estimation(MLE). 

To make a prediction in the binary classification, 
compute s=p(x|y=1)p(y=1)-p(x|y=0)p(y=0), 
when s>=0, predict y to be 1, otherwise, y is 0.

Run `python gda.py` to train and test GDA model on `heart-scale` dataset

Training accuracy : `85.253456%`

Test accuracy : `88.679245%`

##Logistic Regression

lr.py implements `Logistic Regression`(LR) model for binary classification.

Unlike NB and GDA, LR belongs to another family of statistical model: the discriminant model, which tries to model p(y|x) directly.

LR is a very widely-used classification model. It is simple, efficient, easy to train and interpretaion.

Here the CG routine is used again to train the LR model. 

Run `python lr.py` to train and test LR on `heart-scale` dataset.

Training accuracy : 79.723502% (173/217)

Test accuracy : 90.566038% (48/53)

##Softmax Regression

When dealing with a multi-classification problem, we can either adopt the `one-against-all` idea 
by transforming it into a set of binary classification problem, 
or just training the `Softmax Regression` model.

Softmax Regression model is naturely born for the multi-classification problem, 
and can be viewed as an extension of the LR model. 
Training the Softmax model is much the same as LR.

Run `python softmax.py` to train and test LR on the `mnist` hand-written digit recognization dataset.

 
##Support Vector Machines

svm.py implements `Sequential Minimization Optimization`(SMO) and `Primal estimated sub-gradient solver for SVMs`(pegasos) algorithms 
for training `Support Vector Machines`(SVMs).

SMO is an instance of `Coordinate Descent` algorithm. It works in the dual space, so kernel trick is availabel. 
The SMO algorithm reduces the computational complexity by dividing the overall problem into small sub-problems,
and this pair-wise subproblems can by solved analyticaly.

The fully implemented algorithm is repositoried in another GitHub project https://github.com/mazefeng/svm, 
which is written in C++, and the input data format is the same as LibSVM/LibLinear.

This SMO implementation simplify the original one in the following 3 ways:

1. Choose lagrangian multipliers for sub-problems at random instead of using the heuristic methods, this only effect the speed of convergence.
2. Error cache mechanism isn't implemented.
3. Kernel matrix is pre-computed and stored in memory. This couldn't be done for large scale problem.

While pegasos is an gradient-based algorithm, it runs very fast, and can be easily parallelized. 
Pegasos works in primal space, so only linear kernel is available. 

Run `python svm.py` to train and test SVMs on `heart-scale` dataset.

For `Pegasos`, 

Training accuracy : `77.419355%`

Test accuracy : `88.679245%`

For `linear SMO`, 

Training accuracy : 84.792627%

Test accuracy : 86.792453%

For `rbf kernel SMO`, 

Training accuracy : 87.557604%

Test accuracy : 90.566038%`

##AdaBoost

adaboost.py implements `Adaptive Boosting`(AdaBoost) algorithm for classification.

The most famous application of AdaBoost is face recognition: by combining AdaBoost with `Haar-like` features, the face recognition algorithm achieves state-of-the-art accuracy and speed.

AdaBoost boost the classifier accuracy by maintaining a weight distribution on the samples. 
On each round, a weak classifier is trained on the weighted samples. And the weights of samples which are misclassified by the previous weak classifier are increased, the others are decreased. It can be proofed that, after enough round, the loss of AdaBoost go exactly to 0 (without outliar). Of course, this often leads to overfitting.

When making a prediction, all the weak classifier make a weighted majority vote.

In face recognition problem, a `decision stump` is adopted as the weak classifier, which is a decision tree with only 1 level. But AdaBoost itself make no restriction on the weak classifier being used. It only require the weak classifier can be trained on the weighted samples, and many classifier can be modified to handle this situation.

Here we use a weighted version of the `Pocket` as weak classfier, run `python adaboost.py` to train and test AdaBoost on `heart-scale` dataset.

Accuracy for AdaBoost : `86.792453%`

##Matrix Factorization

mf.py implements `matrix factorization`(MF) algorithms for recommendation.

Run `python mf.py` to train and test MF on the `1 million` version of `MovieLens` dataset.

RMSE on the test data is `0.932806` after `5 iterations`.

MF is optimized by `Stochastic Gradient Descent`(SGD) algorithm in mini-batch manner. 
By combining `NumPy` with mini-batch, the training algorithm is very efficient with an affordable memory cost.

##Hidden Markov Model

hmm.py implements `Hidden Markov Models`(HMMs) for solving `sequence labeling` problem.

Run `python hmm.py` to train and test POS-tagger using trigram HMMs on a small corpus.

HMMs is trained by `Maximum Likelihood Estimated`(MLE), and smooth technics is necessary for good performance.

Using HMMs for labeling sequence is a little difficult. The brute force solution is not sufficient enough, 
thus a `Dynamic Programming`(DP) algorithm is used here: the famous `Viterbi` algorithm.

For comparasion, a baseline tagger is also implemented by ignore the state transition probability. 

Accuracy for baseline tagger: `91.661447%`

Accuracy for `Viterbi` tagger: `93.490334%`

<pre><code>
P--V---D------J----------N-------W---V-----V-----I----D---N--------N------,--R---D---J----N---.
|  |   |      |          |       |   |     |     |    |   |        |      |  |   |   |    |   |
It is the right-wing guerrillas who are aligned with the drug traffickers , not the left wing .
</code></pre>

`TODO`: Parallel HMMs training, `Max Entropy Markov Models`(MEMM), `Conditional Random Field`(CRF)

##Neural Network

nn.py implements a three layer `Neural Network model`(NN) for classification.

NN is a powerful model motivated by how brain works. Training a NN is a little complicated. In the feed-forward phrase, training data go through the input layer to the hidden layer, and finally the output layer. The error is computed between the output of the model and the ground-truth label. In the back-propogation phrase, the error is back-propogate reversely, from the output layer to the hidden layer, then to the input layer. When this is done, the gradient with respect to each individual weight is obtained and feed to the optimization routine.

NN show its great power when we carefully tune its parameters. It is powerful, but not "off-the-shelf".For example, due the gradient vanish and explose  phenomenon, we have to carefully initialize the weight and normalize the input, just like what we do in the experiment, normalize each dimension to follow a normal distribution, and randomly initialize each weight to a small random number to do a symmetry-breaking.

## Optimization

Most of the numerical optimization routines can be summarize as following steps:

1. Find the direction.
2. (Optional) Modify this direction using extra infomation
3. Find a step size to go as further as possiblem, often use a line search routine.
4. Repeat step 1-3 until convergence.

Algorithms including step-2 are categorized as second-order methods (such as Newton's method, Quasi-Newton's method),
the others are first-order methods.

The second-order methods converge faster, but they have to store and maintance a Hessian matrix, which is O(n^2) complexity.

And there are methods trying to approximate the Hessian matrix using recent gradients, they belong to the Quasi-Newton's methods, such as Limited-memroy BFGS.

### Stochastic Gradient Descent(SGD)

### Conjugate Gradient method(CG)

### L-BFGS

### OWL-QN

