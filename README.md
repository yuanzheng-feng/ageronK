Machine Learning Notebooks
==========================

[![Gitter](https://badges.gitter.im/ageron/ml-notebooks.svg)](https://gitter.im/ageron/ml-notebooks?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)

This project aims at teaching you the fundamentals of Machine Learning in
python. 

Simply open the [Jupyter](http://jupyter.org/) notebooks you are interested in:

* using [Binder](http://mybinder.org/): [![Binder](http://mybinder.org/badge.svg)](http://mybinder.org/repo/ageron/ml-notebooks)
    * this let's you experiment with the code examples
* or using [jupyter.org's notebook viewer](http://nbviewer.jupyter.org/github/ageron/ml-notebooks/blob/master/index.ipynb)
    * note: [github.com's notebook viewer](https://github.com/ageron/ml-notebooks/blob/master/index.ipynb) also works but it is slower and the math formulas are not displayed correctly
* or by cloning this repository and running Jupyter locally
    * if you prefer this option, follow the installation instructions below.

# Installation

No installation is required, just click the *launch binder* button above, and you're good to go! But if you insist, here's how to install these notebooks on your system.

Obviously, you will need [git](https://git-scm.com/) and [python](https://www.python.org/downloads/) (2 or 3).

First, clone this repository:

    $ cd {your development directory}
    $ git clone https://github.com/ageron/ml-notebooks.git
    $ cd ml-notebooks

If you want an isolated environment, you can use [virtualenv](https://virtualenv.readthedocs.org/en/latest/):

    $ virtualenv env
    $ source ./env/bin/activate

There are different packages for TensorFlow, depending on your platform. Please edit `requirements.txt` using your favorite editor, and make sure only the right one for your platform is uncommented. Default is Python 2, Ubuntu/Linux 64-bits, CPU-only.

Then install the required python packages using pip:

    $ pip install -r requirements.txt

Finally, launch Jupyter:

    $ jupyter notebook

This should start the Jupyter server locally, and open your browser. Click on `index.ipynb` to get started.
