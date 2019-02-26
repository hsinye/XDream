Preprint available at https://www.biorxiv.org/content/10.1101/516484v1

## Introduction
XDream (E**x**tending **D**eepDream with **r**eal-time **e**volution
for **a**ctivity **m**aximization in real neurons)
is a package for visualizing the preferred input of
units in non-differentiable visual systems—such as
the visual cortex—through activation maximization.

Well-known network visualization techniques such as DeepDream
depend on the objective function being differentiable down to
the input pixels. In cases such as the visual cortext in the brain,
such gradients are unavailable.

However, visualization without gradients can be framed as a
black-box optimization problem. Such a problem can be approached
by an iterative process where
1. Images are shown;
2. Each image receives a scalar "score";
3. Scores are used to optimize the images.   

![OptimizerScorer](./illustrations/OptSco.png)

Two optimizers are implemented here: a simple genetic algorithm
and a gradient estimation algorithm based on finite differences.


A further component—generative neural networks—is introduced to make
the search space diverse yet still tractable. Instead of 
optimizing images, the optimization algorithm manipulates
"codes" used to generate images. Since codes are the learned input
to a generative network, searching in code space avoids searching
uninteresting noise images, for example salt-and-pepper noise.

As an example, we use the DeepSiM generators from
[Dosovitskiy & Brox, 2016](https://arxiv.org/abs/1602.02644).
The interface to DeepSiM generators depends on
[`caffe`](http://caffe.berkeleyvision.org), but is modular and
can be replaced by other generative neural networks and indeed any other
image parameterization (e.g.,
[Yamane et al., 2008](https://www.nature.com/articles/nn.2202),
[Mordvintsev et al., 2018](https://distill.pub/2018/differentiable-parameterizations/)
).

![OptimizerScorer](./illustrations/GenOpt.png)


## To Use
Download pretrained generative networks (see next section)
and save them to the paths as defined in `net_catalogue.py`.
Copy `local_settings.example.py` to `local_settings.py`
and modify the contents to match your system.

Experiment modules
(`EphysExperiment` & `CNNExperiment` in `Experiments.py`)
and scripts (`experiment.py` & `experiment_CNN.py`) are included
to exemplify how to define/control an experiment using this package.

To extend this package for use with your experimental system,
at the least you may need to extend the `_get_scores()` method of
`WithIOScorer`. For example, in `EPhysScorer`, we write online
recording data in a .mat file and the `_get_scores()` method
reads it from disk.

Some additional tools are included for creating
the initial generation of codes (for genetic algorithm) and
empirically optimizing hyperparameters.


## Pretrained networks
- The DeepSiM generators can be found here:
https://lmb.informatik.uni-freiburg.de/people/dosovits/code.html.

    To use them here, please
    - Modify the prototxt files to use batch size of 1
    (i.e., modify the first dimension of `input_shape`)
    - Change all `engine: CUDNN` entries to `engine: CAFFE`,
    if applicable
    - Edit layer names in `net_catalogue.py` to match the prototxt 
- To run simulated experiments with CNN models,
the reference caffenet model is needed and can be found here
https://github.com/BVLC/caffe/tree/master/models/bvlc_reference_caffenet.
Other caffe networks can be easily used as well. 
