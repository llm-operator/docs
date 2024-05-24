Architecture
============

.. note::

   Work-in-progress.

Components
----------

LLM Operator provisions the LLM stack consisting of the following micro services:

- Inference Manager
- Job Manager
- Model Manager
- File Manager
- RBAC Manager
- Session Manager

Each manager is responsible for specific feature of LLM services as their name indicates.


Each manager consists of one or more than one K8s deployment. In a
typical configuration, all the services are deployed into a single
Kubernetes cluster, but you can also deploy these services on multiple
Kubernetes clusters (e.g., control plane cluster and GPU worker
clusters).

Optimization Technologies
-------------------------

- Quota management for fine-tuning jobs
- Inference autoscaling with GPU utilization
- Dynamic model loading & offloading in inference
