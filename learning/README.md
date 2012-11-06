Overview
========

The function of this package of Python scripts is to build models for
machine translation (MT) quality estimation (QE). The input files are
a set of instances with features that describe sentence pairs (source
and target sentences). The features can be extracted using the FeatureExtractor
program available with which this program is bundled.


Installation
============

The program itself does not require any installation step, it is just a matter
of running it provided that all the dependencies are installed.


Dependencies
============

All the machine learning algorithms are implemented by the scikit-learn library.
This program provides a command-line interface for some of the implementations
contained in this toolkit. In order to be able to run, the program requires
that the following packages are installed in your Python distribution:

- numpy ( http://scipy.org/Download )
- scikit-learn ( http://scikit-learn.org/stable/install.html )
- pyyaml ( http://pyyaml.org/ )


Running
=======

The program takes only one input parameter, the configuration file. For
example:

python src/learn_model.py config/svr.yaml


Configuration file
==================

The configuration uses the YAML format (http://www.yaml.org/). Its layout is
quite straightforward. It is formed by key and value pairs that map directly
to dictionaries (in Python) or hash tables with string keys. One example is
as follows:

learning:
    method: LassoLars
    parameters:
        alpha: 1.0
        max_iter: 500
        normalize: True
        fit_intercept: True
        fit_path: True
        verbose: False

Each keyword followed by a ":" represents an entry in a hash. In this example,
the dictionary contains an entry "learning" that points to another dictionary
with two entries "method" and "parameters". The values of each entry can be
lists, dictionaries or primitive values like floats, integers, booleans or strings.
Please note that each level in the example above is indented with 4 spaces.

For more information about the YAML format please refer to http://www.yaml.org/ .


Available algorithms
====================

Currently these are the algorithms available in the script:

* SVR: epsilon Support Vector Regression
The parameters exposed in the "Parameters" section of the configuration file are:
	- C
	- epsilon
	- kernel
	- degree
	- gamma
	- tol
	- verbose

Documentation about these parameters is available at
http://scikit-learn.org/stable/modules/generated/sklearn.svm.SVR.html#sklearn.svm.SVR

* SVC: C-Support Vector Classification
The parameters exposed in the "Parameters" section of the configuration file are:
	- C
	- coef0
	- kernel
	- degree
	- gamma
	- tol
	- verbose

Documentation about these parameters is available at
http://scikit-learn.org/stable/modules/generated/sklearn.svm.SVC.html#sklearn.svm.SVC

* LassoCV: Lasso linear model with iterative fitting along a regularization path.
The best model is selected by cross-validation.
The parameters exposed in the "Parameters" section of the configuration file are:
	- eps
	- n_alphas
	- normalize
	- precompute
	- max_iter
	- tol
	- cv
	- verbose

Documentation about these parameters is available at
http://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LassoCV.html#sklearn.linear_model.LassoCV


* LassoLars: Lasso model fit with Least Angle Regression (Lars)
The parameters exposed in the "Parameters" section of the configuration file are:
	- alpha
	- fit_intercept
	- verbose
	- normalize
	- max_iter
	- fit_path

Documentation about these parameters is available at
http://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LassoLars.html#sklearn.linear_model.LassoLars

* LassoLarsCV: Cross-validated Lasso using the LARS algorithm
The parameters exposed in the "Parameters" section of the configuration file are:
    - max_iter
	- normalize
	- max_n_alphas
	- n_jobs
	- cv
	- verbose

Documentation about these parameters is available at
http://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LassoLarsCV.html#sklearn.linear_model.LassoLarsCV


Parameter optimization
======================

It is possible to optimize the parameters of the chosen method. This option is
set by the "optimize" setting under "learning" in the configuration file. The
script uses the scikit-learn's GridSearchCV implementation of grid search to
optimize parameters using cross-validation. To optimize the C, gamma and
epsilon parameters for the SVR, the learning section of the configuration
file could look as follows:

"""
learning:
    method: SVR
optimize:
    kernel: [rbf]
    C: [1, 10, 2]
    gamma: [0.0001, 0.01, 2]
    epsilon: [0.1, 0.2, 2]
    cv: 3
    n_jobs: 1
    verbose: True
"""

The parameter kernel is a list of strings representing the available kernels
implemented by scikit-learn. In this example only the "RBF" kernel is used.

The SVR parameters C, gamma and epsilon are set with lists with 3 indexes:
    - the beginning of the range (begin value included)
    - the end of the range (end value included)
    - the number of samples to be generated within [beginning, end]

The remaining parameters modify the behavior of the GridSearchCV class:
    - 'cv' is the number of cross-validation folds
    - 'n_jobs' is the number of parallel jobs scheduled to run the CV process
    - 'verbose' is a boolean or integer value indicating the level of verbosity

For more information about the GridSearchCV class please refer to
http://scikit-learn.org/stable/modules/generated/sklearn.grid_search.GridSearchCV.html#sklearn.grid_search.GridSearchCV