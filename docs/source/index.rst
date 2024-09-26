Welcome to LLM Operator documentation!
======================================

.. raw:: html

   <p style="text-align:center">
   <a class="github-button" href="https://github.com/llm-operator/llm-operator" data-show-count="true" data-size="large" aria-label="Star llm-operator/llm-operator on GitHub">Star</a>
   <script async defer src="https://buttons.github.io/buttons.js"></script>
   </p>

   <p style="text-align:center">
   <strong>Transform your GPU clusters into a powerhouse for generative AI workloads</strong>
   </p>


Do you want an API compatible with OpenAI to leverage the extensive GenAI ecosystem?
If so, LLM Operator is what you need.
It instantly builds a software stack that provides an OpenAI-compatible API for inference, fine-tuning,
and model management. Pleas see the presentation below to learn more:

.. raw:: html

   <p style="text-align:center">
   <iframe src="https://www.slideshare.net/slideshow/embed_code/key/4h5i9Kg8WQatub?hostedIn=slideshare&page=upload" width="476" height="400" frameborder="0" marginwidth="0" marginheight="0" scrolling="no"></iframe>
   </p>


If you want to play with LLM Operator, check out :doc:`playground`. You can install LLM Operator in a Kind cluster by following the steps described there.
You can also just directly move to the :doc:`install` section and install LLM Operator to your Kubernetes clusters!


Contents
--------

.. toctree::
   :caption: Getting Started

   playground
   install
   tutorials

.. toctree::
   :caption: Features

   inference
   models
   rag
   fine_tuning
   jupyter_notebook
   training
   gpu_showback
   user_management
   access_control

.. toctree::
   :caption: Integrations

   openwebui
   continue
   mlflow
   wandb

.. toctree::
   :caption: Advanced Topics

   architecture
   multi_cluster_deployment
   hosted_control_plane

.. toctree::
   :caption: Development Plan

   roadmap
