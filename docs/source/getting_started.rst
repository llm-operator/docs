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

   helm upgrade --install \
       --namespace <namespace> --create-namespace \
       --values <values.yaml> \
       llm-operator oci://public.ecr.aws/v8n3t7y5/llm-operator-charts/llm-operator

.. note::

   Starting from Helm v3.8.0, the OCI registry is supported by default. If you are using an older version, please upgrade to v3.8.0 or later.
   For more details, please refer to `Helm OCI-based registries <https://helm.sh/docs/topics/registries/>`_.

.. note::

   If you are getting a 403 forbidden error, please try ``docker logout public.ecr.aws``. Please see `AWS document <https://docs.aws.amazon.com/AmazonECR/latest/public/public-troubleshooting.html>`_ for more details.


Once installation completes, you can interact with the API endpoint using the `OpenAI Python library <https://github.com/openai/openai-python>`_., running our CLI,
or direclty hitting the endpoint.

You can also download the CLI and use it.

.. code-block:: console

   export ARCH=<e.g., linux-amd64, darwin-arm64>
   curl --remote-name http://llm-operator-artifacts.s3.amazonaws.com/artifacts/cli/0.89.0/"${ARCH}"/llmo
   chmod u+x ./llmo


The next tutorial section goes through an example installation to a `Kind <https://kind.sigs.k8s.io/>`_ cluster
running on an EC2 instance.
