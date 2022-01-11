# Chapter 15 - Support vector machines and machine learning on documents

state-of-the-art classifiers:

- support vector machines
- boosted decision trees
- regularized logistic regression
- neural networks
- random forests

## Support vector machines (SVM): The linearly separable case

There are lots of possible linear separators for two-class, separable training data sets. SVM search for the best linear separator according to following criterion:
to be looking for a decision surface that is maximally far away from any data point

**margin:** distance from the decision surface to the closest data point

**support vectors:** the decision function for an SVM is fully specified by a (usually small) subset
of the data which defines the position of the separator. These points are called support vectors.

![Screen Shot 2022-01-10 at 18.25.38.png](Chapter%2015%20-%20Support%20vector%20machines%20and%20machine%20l%205120b888320a404d8d885364f2531633/Screen_Shot_2022-01-10_at_18.25.38.png)

### pros of SVM:

- safety margin: a slight error in measurement or a slight document variation will not cause a misclassification.
- the memory capacity of the model has been decreased, and hence we expect that its ability to correctly generalize to test data is increased

### **formalize SVM in algebra:**

decision hyperplane:

![Screen Shot 2022-01-09 at 18.27.06.png](Chapter%2014%20-%20Vector%20space%20classification%20b9d257ec7a8d4a18bf5dbe242634d267/Screen_Shot_2022-01-09_at_18.27.06.png)

w is called **weight vector** which is perpendicular to the hyper lane

For SVMs, the two data classes are named +1 and −1, and the intercept term is always explicitly represented as b. (عرض از مبدا)

The linear classifier is then:

![Screen Shot 2022-01-10 at 18.39.35.png](Chapter%2015%20-%20Support%20vector%20machines%20and%20machine%20l%205120b888320a404d8d885364f2531633/Screen_Shot_2022-01-10_at_18.39.35.png)

**functional margin:** we define functional margin of of the ith example x_i with respect to a hyperplane <w, b> as the quantity $y_i(\vec{w}^T\vec{x}_i+b)$.

The functional margin of a data set with respect to a decision surface is then twice the functional margin of any of the points in the data set with minimal functional margin.

problem: the value is underconstrained, we can scale w and b

we will compute real distance of x to hyperplane <w, b>:

![Screen Shot 2022-01-10 at 18.49.16.png](Chapter%2015%20-%20Support%20vector%20machines%20and%20machine%20l%205120b888320a404d8d885364f2531633/Screen_Shot_2022-01-10_at_18.49.16.png)

**geometric margin** of the classifier is the maximum width of the band that can be drawn separating the support vectors of the two classes.

That is, it is twice the minimum value over data points for r given in above Equation.

if we set |w|=1 then function margin = geometric margin

:چون می‌تونیم فانکشنال مارجین رو اسکیل کنیم، فرض می‌کنیم که‫ w رو یه جوری انتخاب کردیم که برای هر نقطه تو دیتا

 $y_i(\vec{w}^T\vec{x}_i+b)$‌≥1

‫و برای یه نقطه‌ای هم حتما این نابرابری، برابری باشه که اون نقطه می‌شه یکی از ساپورت وکتورها.

‫در نتیجه جئومتریک مارجینمون می‌شه:

2/|w|

‫و در نهایت می‌تونیم این جوری SVM رو به عنوان یک مسئله‌ی مینیمایزیشن بنویسیم:

![Screen Shot 2022-01-10 at 20.10.30.png](Chapter%2015%20-%20Support%20vector%20machines%20and%20machine%20l%205120b888320a404d8d885364f2531633/Screen_Shot_2022-01-10_at_20.10.30.png)

→ **Quadratic optimization** (درجه دو)

## Extensions to the SVM model

### Soft margin classification

If the training set D is not linearly separable, the standard approach is to allow the fat decision margin to make a few mistakes

**slack variables ξi:** A non-zero value for ξ_i allows x_i to not meet the margin requirement at a cost proportional to the value of ξ_i.

![Screen Shot 2022-01-10 at 20.31.20.png](Chapter%2015%20-%20Support%20vector%20machines%20and%20machine%20l%205120b888320a404d8d885364f2531633/Screen_Shot_2022-01-10_at_20.31.20.png)

formulation of the SVM optimization problem with slack variables:

![Screen Shot 2022-01-10 at 20.33.24.png](Chapter%2015%20-%20Support%20vector%20machines%20and%20machine%20l%205120b888320a404d8d885364f2531633/Screen_Shot_2022-01-10_at_20.33.24.png)

The parameter C is a **regularization term**, which provides a way to control overfitting:

**Time complexity:**

![Screen Shot 2022-01-10 at 20.40.00.png](Chapter%2015%20-%20Support%20vector%20machines%20and%20machine%20l%205120b888320a404d8d885364f2531633/Screen_Shot_2022-01-10_at_20.40.00.png)

### Multiclass SVMs

using two-class classifiers SVM:

- build |C| one-versus-rest (commonly referred to as “one-versus-all” or **OVA** classification)
- build a set of one-versus-one classifiers, and to choose the class that is selected by the most classifiers

multiclass SVMs:

we build a two-class classifier over a feature vector $\Phi(\vec{x}, y)$  derived from the pair consisting of the input features and the class of the datum. At test time, the classifier chooses the class $y = argmax_{y'}\vec{w}^T\Phi(\vec{x}, y')$.

→ **structural SVMs**

### Nonlinear SVMs

One way to solve this problem is to map the data on to a higher dimensional space and then to use a linear classifier in the higher dimensional space. 

![Screen Shot 2022-01-10 at 20.54.30.png](Chapter%2015%20-%20Support%20vector%20machines%20and%20machine%20l%205120b888320a404d8d885364f2531633/Screen_Shot_2022-01-10_at_20.54.30.png)

→ map the original feature space to some higher-dimensional feature space where the training set is separable.

**the kernel trick:** 

حال ندارم - ص۳۳۲

A **kernel function** K is such a function that corresponds to a dot product in some expanded feature space.

## Issues in the classification of text documents

### **Choosing what kind of classifier to use**

- no labeled training data, existing staff knowledgeable:
    - hand-written rules
- little data:
    - supervised classifier →a classifier with high bias
    - semi-supervised training methods; bootstrapping or the EM algorithm
        - semi-supervised SVM → **transductive SVMs**
- get more labeled data:
    - active learning: where a system is built which decides which documents a human should label.
- reasonable amount of labeled data:
    - use the best model, for example SVM
- huge amount of labeled data:
    - the choice of classifier probably has little effect
    - best to choose a classifier based on the scalability of training or even runtime efficiency.
    

### Improving classifier performance

**Large and difficult category taxonomies**

- hierarchical classification
- use of aggressive feature selection
- a small boost in classification accuracy by combining multiple classifiers

**Features for text**

default: terms as features

**feature engineering:** the process of selecting, manipulating, and transforming raw data into features that can be used in supervised learning

- Classification problems contain large numbers of terms which can be conveniently grouped e.x. year, ISBN, numbers, dates, etc. 
→  converting these into distinguished tokens 
→ reduce the number of features and feature sparseness
→ enhance effectiveness and classifier speed
- increase number of features by matching parts of words, and by matching selected multiword patterns that are particularly discriminative.
- stemming, lowercasing

**Document zones in text classification**

- upweighting title words - double the weight of title words
- Separate feature spaces for document zones
- Connections to text summarization

## Machine learning methods in ad hoc information retrieval

the scoring function is a linear combination of two factors:

1. the vector space cosine similarity between query and document
2. the minimum window width ω within which the query terms lie

todo todo todo