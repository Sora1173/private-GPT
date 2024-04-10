# Introduction
This will be the guide on how to setup privateGPT, installing this took a lot of our time and we did a lot of configuration so we suggest that you clone the original repository of privateGPT. This is our codebase and it is open-source so feel free to jump in.
>You can check the documentation here: https://docs.privategpt.dev/
# Prerequisites
Make sure you have the latest version of Ubuntu WSL installed. You can choose from versions such as Ubuntu-22–04–3 LTS or Ubuntu-22–04–6 LTS available on the Windows Store. (You can use WSL -)

Updating Ubuntu
`sudo apt-get update`
`sudo apt-get upgrade`
`sudo apt-get install build-essential`
ℹ️ “upgrade” is very important as python stuff will explode later if you don’t

# Cloning the PrivateGPT repo (You can choose to clone our repo but we suggest that you use the main repository instead)
`git clone https://github.com/Sora1173/private-GPT`
`git clone https://github.com/imartinez/privateGPT`

# Setting Up Python Environment
To manage Python versions, we’ll use pyenv. Follow the commands below to install it and set up the Python environment:
`sudo apt-get install git gcc make openssl libssl-dev libbz2-dev libreadline-dev libsqlite3-dev zlib1g-dev libncursesw5-dev libgdbm-dev libc6-dev zlib1g-dev libsqlite3-dev tk-dev libssl-dev openssl libffi-dev`

`curl https://pyenv.run | bash`

`export PATH="/home/$(whoami)/.pyenv/bin:$PATH"`

Add the following lines to your .bashrc file:
`export PYENV_ROOT="$HOME/.pyenv"`
`[[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"`
`eval "$(pyenv init -)"`

Reload your terminal
`source .bashrc`

Install important missing pyenv stuff
`sudo apt-get install lzma`
`sudo apt-get install liblzma-dev`

Install Python 3.11 and set it as the global version:
`pyenv install 3.11`
`pyenv global 3.11`
`pip install pip --upgrade`
`pyenv local 3.11`

# Poetry Installation
Install poetry to manage dependencies:
`curl -sSL https://install.python-poetry.org | python3 -`

Add the following line to your .bashrc:
`export PATH="/home/<YOU USERNAME>/.local/bin:$PATH"`
ℹ️ Replace by your WSL username ($ whoami)

Reload your configuration
`source ~/.bashrc`
`poetry --version` # run this command, it should display something without errors

# Installing PrivateGPT Dependencies
Navigate to the PrivateGPT directory and install dependencies:
`cd privateGPT`
`poetry install --extras "ui embeddings-huggingface llms-llama-cpp vector-stores-qdrant"`

# Nvidia Drivers Installation
Visit Nvidia’s official website to download and install the Nvidia drivers for WSL. Choose Windows > x86_64 > WSL-Ubuntu > 2.0 > deb (network)

Follow the instructions provided on the page.

Add the following lines to your .bashrc:
`export PATH="/usr/local/cuda-12.4/bin:$PATH"`
`export LD_LIBRARY_PATH="/usr/local/cuda-12.4/lib64:$LD_LIBRARY_PATH"`
ℹ️ Maybe check the content of “/usr/local” to be sure that you do have the “cuda-12.4” folder. Yours might have a different version.

Reload your configuration and check that all is working as expected
`source ~/.bashrc`
`nvcc --version`
`nvidia-smi.exe`
ℹ️ “nvidia-smi” isn’t available on WSL so just verify that the .exe one detects your hardware. Both commands should displayed gibberish but no apparent errors.

Building and Running PrivateGPT
Finally, install LLAMA CUDA libraries and Python bindings:
`CMAKE_ARGS='-DLLAMA_CUBLAS=on' poetry run pip install --force-reinstall --no-cache-dir llama-cpp-python`

Let private GPT download a local LLM for you (mixtral by default):
`poetry run python scripts/setup`

To run PrivateGPT, use the following command:
`make run`

This will initialize and boot PrivateGPT with GPU support on your WSL environment.

ℹ️ You should see “blas = 1” if GPU offload is working.
...............................................................................................
>llama_new_context_with_model: n_ctx      = 3900
llama_new_context_with_model: freq_base  = 1000000.0
llama_new_context_with_model: freq_scale = 1
llama_kv_cache_init:      CUDA0 KV buffer size =   487.50 MiB
llama_new_context_with_model: KV self size  =  487.50 MiB, K (f16):  243.75 MiB, V (f16):  243.75 MiB
llama_new_context_with_model: graph splits (measure): 3
llama_new_context_with_model:      CUDA0 compute buffer size =   275.37 MiB
llama_new_context_with_model:  CUDA_Host compute buffer size =    15.62 MiB
AVX = 1 | AVX_VNNI = 0 | AVX2 = 1 | AVX512 = 0 | AVX512_VBMI = 0 | AVX512_VNNI = 0 | FMA = 1 | NEON = 0 | ARM_FMA = 0 | F16C = 1 | FP16_VA = 0 | WASM_SIMD = 0 | BLAS = 1 | SSE3 = 1 | SSSE3 = 1 | VSX = 0 |
18:50:50.097 [INFO    ] private_gpt.components.embedding.embedding_component - Initializing the embedding model in mode=local 

ℹ️ Go to` 127.0.0.1:8001` in your browser