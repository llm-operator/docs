Getting Started
===============


Prerequisites
-------------

LLM Operator builds the LLM stack in a single Kubernetes cluster or in multiple Kubernetes clusters. It
requires the following components are installed in a target Kubernetes cluster:

- Ingress controller
- SQL database (to store jobs/models/files metadata)
- S3-compatible object store (to store training files and LLM models)

An SQL database and an S3-compatible object store can be inside the same Kubernetes cluster or outside. For example, you can deploy a Postgres database
or `MinIO <https://min.io/>`_ into the same Kubernetes cluster, or you can use AWS RDS or AWS S3.


Installation
------------

We provide a Helm chart for installing LLM Operator. You can obtain the Helm chart from our repository and install.

.. code-block:: console

   helm repo add llm-operator http://llm-operator-charts.s3-website-us-west-2.amazonaws.com/
   helm repo update
   helm upgrade --install -n <namespace> --create-namespace llm-operator llm-operator/llm-operator -f <values.yaml>


Once installation completes, you can interact with the API endpoint using the `OpenAI Python library <https://github.com/openai/openai-python>`_., running our CLI,
or direclty hitting the endpoint.

You can also download the CLI and use it.

.. code-block:: console

   export ARCH=<e.g., linux-amd64, darwin-arm64>
   curl --remote-name http://llm-operator-artifacts.s3.amazonaws.com/artifacts/cli/0.87.0/"${ARCH}"/llmo
   chmod u+x ./llmo


The next tutorial section goes through an example installation to a `Kind <https://kind.sigs.k8s.io/>`_ cluster
running on an EC2 instance.
