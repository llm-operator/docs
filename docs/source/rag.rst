Retrieval-Augmented Generation (RAG)
====================================

.. note::

   Work-in-progress.

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
