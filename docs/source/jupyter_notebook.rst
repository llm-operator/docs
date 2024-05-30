Jupyter Notebook
================

.. note::

   Work-in-progress.

LLM Operator allows users to run a Jupyter Notebook in a Kubernetes cluster. This functionality
is useful when users want to run ad-hoc Python scripts that requires GPU.


Creating a Jupyter Notebook
---------------------------

To create a Jupyter Notebook, run:

.. code-block:: console

   llmo workspace notebooks create --name my-test-notebook

By default, there is no GPU is allocated to the Jupyter Notebook pod.
