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
--------------------------------------------

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

.. note::

    See ``variables.tf`` for other customizable and default values.

Then, run the following Terraform commands to initialize and create an EC2 instance. This will approximately take 10 minutes.

.. code-block:: console

   terraform init
   terraform apply -var-file=local.tfvars


If you want to run only Ansible playbook, you can just run ``ansible-playbook -i inventory.ini playbook.yml``.


Step 4: Interact with the LLM Service
-------------------------------------

You can access the API endpoint by establishing a SSH port-forwarding.

.. note::

   ansible all -i inventory.ini --ssh-extra-args="-L8080:localhost:80" -m shell -a "sleep infinity"

With the above command, you can hit the API via ``http://localhost:8080``. You can directly hit the endpoint via `curl`
or other commands, or you can use `the OpenAI Python library <https://github.com/openai/openai-python>`_.

Here is an example command for listing all available models and hitting the chat endpoint.

.. code-block:: console

   curl http://localhost:8080/v1/models | jq
   curl --request POST "http://localhost/v1/chat/completions" -d '{"model": "google-gemma-2b-it-q4", "messages": [{"role": "user", "content": "What is k8s?"}]}'

Here is an example Python code for hitting the chat endpoint.

.. code-block:: python

   from openai import OpenAI

   client = OpenAI(
     base_url="http://localhost:80/v1",
     api_key="<dummy>"
   )

   completion = client.chat.completions.create(
     model="google-gemma-2b-it-q4",
     messages=[
       {"role": "user", "content": "What is k8s?"}
     ]
   )
   print(completion.choices[0].message)

Step 5: Clean up
----------------

Run the following command to destroy the EC2 instance.

.. code-block:: console

   terraform destroy -var-file=local.tfvars
