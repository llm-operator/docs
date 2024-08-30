Tutorial: Install to a Kind Cluster
===================================

In this tutorial, we provision an EC2 instance, build a `Kind <https://kind.sigs.k8s.io/>`_ cluster, and
deploy LLM Operator and other required components.

Once all the setup completes, you can interact with the LLM service
by directly hitting the API endpoints or using `the OpenAI Python library <https://github.com/openai/openai-python>`_.


Step 1: Install Terraform and Ansible
-------------------------------------

We use Terraform and Ansible. Follow the links to install if you haven't.

- `Terraform <https://developer.hashicorp.com/terraform/install>`_
- `Ansible <https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html>`_
- `kubernetes.core.k8s module for Ansible <https://docs.ansible.com/ansible/latest/collections/kubernetes/core/k8s_module.html>`_

To install ``kubernetes.core.k8s`` module, run the following command:

.. code-block:: console

   ansible-galaxy collection install kubernetes.core


Step 2: Clone the LLM Operator Repository
-----------------------------------------

We use the Terraform configuration and Ansible playbook in the `the LLM Operator repository <https://github.com/llm-operator/llm-operator>`_.
Run the following commands to clone the repo and move to the directory where the Terraform configuration file is stored.

.. code-block:: console

   git clone https://github.com/llm-operator/llm-operator.git
   cd llm-operator/aws


Step 3: Run Terraform
---------------------

First create a ``local.tfvars`` file for your deployment. Here is an example.

.. code-block:: console

   project_name = "<instance-name> (default: "llm-operator-demo")"
   profile      = "<aws-profile>"

   public_key_path  = "</path/to/public_key_path>"
   private_key_path = "</path/to/private_key_path>"
   ssh_ip_range     = "<ingress CIDR block for SSH (default: "0.0.0.0/0")>"


``profile`` is an AWS profile that is used to create an EC2 instance. ``public_key_path`` and ``private_key_path`` specify
an SSH key used to access the EC2 instance.

.. note::

    See ``variables.tf`` for other customizable and default values.

Then, run the following Terraform commands to initialize and create an EC2 instance. This will approximately take 10 minutes.

.. code-block:: console

   terraform init
   terraform apply -var-file=local.tfvars

.. note::

   If you want to run only Ansible playbook, you can just run ``ansible-playbook -i inventory.ini playbook.yml``.

Once the deployment completes, a Kind cluster is built in the EC2 instance and LLM Operator is running in the cluster.
It will take another about five minutes for LLM Operator to load base models, but you can move to the next step meanwhile.


Step 4: Set up SSH Connection
-----------------------------

You can access the API endpoint and Grafana by establishing SSH port-forwarding.

.. code-block:: console

   ansible all \
     -i inventory.ini \
     --ssh-extra-args="-L8080:localhost:80 -L8081:localhost:8081" \
     -a "kubectl port-forward -n monitoring service/grafana 8081:80"

With the above command, you can hit the API via ``http://localhost:8080``. You can directly hit the endpoint via `curl`
or other commands, or you can use the `OpenAI Python library <https://github.com/openai/openai-python>`_.

You can also reach Grafana at ``http://localhost:8081``. The login username is ``admin``, and the password can be obtained
with the following command:

.. code-block:: console

   ansible all \
     -i inventory.ini \
     -a "kubectl get secrets -n monitoring grafana -o jsonpath='{.data.admin-password}'" | tail -1 | base64 --decode; echo


Step 5: Obtain an API Key
-------------------------

To access LLM service, you need an API key. You can download the LLM Operator CLI and use that to login the system,
and obtain the API key.

.. code-block:: console

   # Download the binary.
   export ARCH=<e.g., linux-amd64, darwin-arm64>
   curl --remote-name http://llm-operator-artifacts.s3.amazonaws.com/artifacts/cli/0.118.0/"${ARCH}"/llmo
   chmod u+x ./llmo

   # Login. Please see below for the details.
   ./llmo auth login

   # Create an API key.
   ./llmo auth api-keys create my-key


``llmo auth login`` will ask for the endpoint URL and the issuer URL. Please use the default values for them
(``http://localhost:8080/v1`` and ``http://kong-proxy.kong/v1/dex``).

Then the command will open a web browser to login. Please use the following username and the password.

* Username: ``admin@example.com``
* Password: ``password``

The output of ``llmo auth api-keys create`` contains the secret of the created API key. Please save the value
in the environment variable to use that in the following step:


.. code-block:: console

     export LLM_OPERATOR_TOKEN=<Secret obtained from llmo auth api-keys create>


Step 6: Interact with the LLM Service
-------------------------------------

There are mainly three ways to interact with the LLM service.

The first option is to use the CLI. Here are examle commands:

.. code-block:: console

   ./llmo models list

   ./llmo chat completions create --model google-gemma-2b-it-q4 --role user --completion "What is k8s?"


.. code-block:: console

   curl \
     --header "Authorization: Bearer ${LLM_OPERATOR_TOKEN}" \
     --header "Content-Type: application/json" \
     http://localhost:8080/v1/models | jq

   curl \
     --request POST \
     --header "Authorization: Bearer ${LLM_OPERATOR_TOKEN}" \
     --header "Content-Type: application/json" \
     --data '{"model": "google-gemma-2b-it-q4", "messages": [{"role": "user", "content": "What is k8s?"}]}' \
     http://localhost:8080/v1/chat/completions

The second option is to run the ``curl`` command and hit the API endpoint.
Here is an example command for listing all available models and hitting the chat endpoint.

The third option is to use Python. Here is an example Python code for hitting the chat endpoint.

.. code-block:: python

   from os import environ
   from openai import OpenAI

   client = OpenAI(
     base_url="http://localhost:8080/v1",
     api_key=environ["LLM_OPERATOR_TOKEN"]
   )

   completion = client.chat.completions.create(
     model="google-gemma-2b-it-q4",
     messages=[
       {"role": "user", "content": "What is k8s?"}
     ],
     stream=True
   )
   for response in completion:
     print(response.choices[0].delta.content, end="")
   print("\n")

We have a Jupyter Notebook that goes through all the functionalites. Please download the Notebook
from https://github.com/llm-operator/llm-operator/blob/main/tutorial/getting_started.ipynb to play around it.

Step 7: Clean up
----------------

Run the following command to destroy the EC2 instance.

.. code-block:: console

   terraform destroy -var-file=local.tfvars
