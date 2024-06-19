Multi-Cluster Deployment
========================

.. note::

   Work-in-progress.

LLM Operator deploys Kubernetes deployments to provision the LLM
stack. In a typical configuration, all the services are deployed into
a single Kubernetes cluster, but you can also deploy these services on
multiple Kubernetes clusters. For example, you can deploy a control
plane component in a CPU K8s cluster and deploy the rest of the
components in GPU compute clusters.


Deploying Control Plane Components
----------------------------------

You can deploy only Control Plane components by setting ``tags.worker=false``:

.. code-block:: console

   helm upgrade \
     --install \
     --create-namespace \
     --namespace <namespace> \
     llm-operator \
     oci://public.ecr.aws/v8n3t7y5/llm-operator-charts/llm-operator \
     --values <values.yaml> \
     --set tags.worker=false


Deploying Worker Components
---------------------------

To deploy LLM Operator to a worker GPU cluster, you first need to obtain a registration key for the cluster.

.. code-block:: console

   llmo clusters register <cluster-name>


The command generates a new registration key.

Then you need to make LLM Operator worker components to use the registration key
when making gRPC calls to the control plane.

To make that happen, you first need to create a K8s secret.

.. code-block:: console

   REGISTRATION_KEY=clusterkey-...

   kubectl create secret generic \
     -n llm-operator \
     cluster-registration-key \
     --from-literal=regKey="${REGISTRATION_KEY}"


The secret needs to be created in a namespace where LLM Operator will be deployed.

When installing the Helm chart for the worker components, you need to specify the following in ``values.yaml``:

.. code-block:: yaml

  worker:
    registrationKeySecret:
      name: cluster-registration-key
      key: regKey

``tags.worker=true` also need to be set:

.. code-block:: console

   helm upgrade \
     --install \
     --create-namespace \
     --namespace <namespace> \
     llm-operator \
     oci://public.ecr.aws/v8n3t7y5/llm-operator-charts/llm-operator \
     --values <values.yaml> \
     --set tags.control-plane=false
