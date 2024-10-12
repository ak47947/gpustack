# Inference Backends

GPUStack supports the following inference backends:

- llama-box
- vLLM

When users deploy a model, the backend is selected automatically based on the following criteria:

- If the model is a [GGUF](https://github.com/ggerganov/ggml/blob/master/docs/gguf.md) model, llama-box is used.
- Otherwise, vLLM is used.

## llama-box

[llama-box](https://github.com/gpustack/llama-box) is a LLM inference server based on [llama.cpp](https://github.com/ggerganov/llama.cpp).

### Supported Platforms

The llama-box backend works on a wide range of platforms, including Windows, MacOS, and Linux.

### Supported Models

Please refer to the list of supported models in [README](https://github.com/ggerganov/llama.cpp#description) of llama.cpp project.

### Supported Features

#### Allow CPU Offloading

After enabling CPU offloading, GPUStack prioritizes loading as many layers as possible onto the GPU to optimize performance. If GPU resources are limited, some layers will be offloaded to the CPU, with full CPU inference used only when no GPU is available.

#### Allow Distributed Inference Across Workers

Enable distributed inference across multiple workers. The primary Model Instance will communicate with backend instances on one or more others workers, offloading computation tasks to them.

### Parameters Reference

See the full list of supported parameters for llama-box [here](https://github.com/gpustack/llama-box#usage).

## vLLM

[vLLM](https://github.com/vllm-project/vllm) is a high-throughput and memory-efficient LLMs inference engine. It is a popular choice for running LLMs in production. vLLM seamlessly supports most state-of-the-art open-source models, including: Transformer-like LLMs (e.g., Llama), Mixture-of-Expert LLMs (e.g., Mixtral), Embedding Models (e.g. E5-Mistral), Multi-modal LLMs (e.g., LLaVA)

By default, GPUStack estimates the VRAM requirement for the model instance based on the model's metadata. You can customize the parameters to fit your needs. The following vLLM parameters might be useful:

- `--gpu-memory-utilization` (default: 0.9): The fraction of GPU memory to use for the model instance.
- `--max-model-len`: Model context length. For large-context models, GPUStack automatically sets this parameter to `8192` to simplify model deployment, especially in resource constrained environments. You can customize this parameter to fit your needs.
- `--tensor-parallel-size`: Number of tensor parallel replicas. By default, GPUStack sets this parameter given the GPU resources available and the estimation of the model's memory requirement. You can customize this parameter to fit your needs.

For more details, please refer to [vLLM documentation](https://docs.vllm.ai/en/stable/serving/openai_compatible_server.html#command-line-arguments-for-the-server).

### Supported Platforms

The vLLM backend works on Linux.

!!! note

    1. When users install GPUStack on amd64 Linux using the installation script, vLLM is automatically installed.
    2. When users deploy a model using the vLLM backend, GPUStack sets worker label selectors to `{"os": "linux", "arch": "amd64"}` by default to ensure the model instance is scheduled to proper workers. You can customize the worker label selectors in the model configuration.

### Supported Models

Please refer to the vLLM [documentation](https://docs.vllm.ai/en/stable/models/supported_models.html) for supported models.

### Supported Features

#### Multimodal Language Models

vLLM supports multimodal language models listed [here](https://docs.vllm.ai/en/stable/models/supported_models.html#multimodal-language-models). When users deploy a vision language model using the vLLM backend, image inputs are supported in the chat completion API.

### Parameters Reference

See the full list of supported parameters for vLLM [here](https://docs.vllm.ai/en/stable/serving/openai_compatible_server.html#command-line-arguments-for-the-server).