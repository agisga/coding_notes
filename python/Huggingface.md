- To downloan a model from Huggingface from within the Python REPL, do:

```
from huggingface_hub import snapshot_download
model_name = "microsoft/Phi-3-medium-128k-instruct"
local_directory = "./Phi-3-medium-128k-instruct"
snapshot_download(repo_id=model_name, local_dir=local_directory)

# or when need to specify a specific revision:

from huggingface_hub import snapshot_download
model_name = "turboderp/Llama-3-70B-Instruct-exl2"
local_directory = "./exl2-models/Llama-3-70B-Instruct-6.0bpw-exl2"
snapshot_download(repo_id=model_name, local_dir=local_directory, revision="6.0bpw")
```