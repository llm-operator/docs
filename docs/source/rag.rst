Retrieval-Augmented Generation (RAG)
====================================

.. note::

  This page describes how to use RAG with LLM Operator.

Creating a Vector Store
-----------------------

You can use the OpenAI Python library to create a vector store
and create files in the vector store. Here is an example script:


.. code-block:: python

   from openai import OpenAI

   client = OpenAI(
     base_url="<LLM Operator Endpoint URL>",
     api_key="<LLM Operator API key>"
   )

   filename = "llm_operator_overview.txt"
   with open(filename, "w") as fp:
     fp.write("LLM Operator builds a software stack that provides LLM as a service. It provides the OpenAI-compatible API.")
   file = client.files.create(
     file=open(filename, "rb"),
     purpose="assistants",
   )
   print("Uploaded file. ID=%s" % file.id)

   vs = client.beta.vector_stores.create(
     name='Test vector store',
   )
   print("Created vector store. ID=%s" % vs.id)

   vfs = client.beta.vector_stores.files.create(
     vector_store_id=vs.id,
     file_id=file.id,
   )
   print("Created vector store file. ID=%s" % vfs.id)

Once the files are added into vector store, you can run the completion request with the RAG model.

.. code-block:: python

   from openai import OpenAI

   client = OpenAI(
     base_url="<Base URL (e.g., http://localhost:8080/v1)>",
     api_key="<API key secret>"
   )

   completion = client.chat.completions.create(
     model="google-gemma-2b-it-q4",
     messages=[
       {"role": "user", "content": "What is LLM Operator?"}
     ],
     tool_choice = {
      "choice": "auto",
      "type": "function",
      "function": {
        "name": "rag"
      }
    },
    tools = [
      {
        "type": "function",
        "function": {
          "name": "rag",
          "parameters": "{\"vector_store_name\":\"Test vector store\"}"
        }
      }
    ],
     stream=True
   )
   for response in completion:
     print(response.choices[0].delta.content, end="")
   print("\n")

If you want to hit the API endpoint directly, you can use ``curl``. Here is an example.

.. code-block:: console

   curl \
     --request POST \
     --header "Authorization: Bearer ${LLM_OPERATOR_TOKEN}" \
     --data '{
      "model": "google-gemma-2b-it-q4",
      "messages": [{"role": "user", "content": "What is LLM Operator?"}],
      "tool_choice": {
        "choice": "auto",
        "type": "function",
        "function": {
          "name": "rag"
        }
      },
      "tools": [{
        "type": "function",
        "function": {
        "name": "rag",
          "parameters": "{\"vector_store_name\":\"Test vector store\"}"
        }
    }]}' \
    http://localhost:8080/v1/chat/completions
