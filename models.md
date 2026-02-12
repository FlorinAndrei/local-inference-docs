# Models

You will use open-weights LLMs that you can download for free. For general-purpose inference, many popular LLMs will work well, pick the biggest that will fit your VRAM / unified RAM:

https://ollama.com/search

I like gemma3:27b for general purpose inference because it's articulate and expressive, like Claude. But any popular model will work well.

For coding, some LLMs are better than others. Feel free to try different models, but right now (early 2026) these are good places to start:

- qwen3-coder-next
- glm-4.7-flash
- gpt-oss

# Ollama

You need to load the model into RAM, and expose it via an API. Many apps can do this, but the easiest to use by far is Ollama:

https://ollama.com/

Installation is very simple. They maintain a library of good models in the GGUF format, already quantized. They have good default settings.

On macOS and Windows, just install the Ollama app.

On Linux, it's best to run it in Docker:

- install the NVIDIA drivers (test with `nvidia-smi`)
- install Docker Engine https://docs.docker.com/engine/install/
- install `nvidia-container-toolkit`

Create an executable script called `~/bin/update-ollama.sh` like this:

```
#!/usr/bin/env bash

docker stop ollama
docker rm ollama
docker pull ollama/ollama:latest

# host.docker.internal is the actual host system (optional)
docker run -d --restart always \
    --gpus=all \
    --runtime=nvidia \
    -p 11434:11434 \
    --add-host=host.docker.internal:host-gateway \
    -v ollama:/root/.ollama \
    --name ollama \
    --env OLLAMA_FLASH_ATTENTION=1 \
    ollama/ollama:latest
```

Run this script once a week, it will update and re-launch Ollama. The container is persistent across reboots.

Ollama runs in a container. To be able to invoke the `ollama` command as usual from the host, put this in `~/.bash_aliases`:

```
ollama() {
    if [ -t 0 ]; then
	# this is a terminal, allocate a pseudo-TTY
        docker exec -it ollama ollama "$@"
    else
	# this is not a terminal, do not allocate a pseudo-TTY
        docker exec -i ollama ollama "$@"
    fi
}
```

To download / update models, something like this sequence works well on macOS, Linux, and Windows:

```
ollama pull glm-4.7-flash:latest
ollama pull gpt-oss:20b
ollama pull gpt-oss:120b
ollama pull qwen3-coder-next:latest
```

Put it in a script, re-run it infrequently - sometimes the newer models in the Ollama repo get small tweaks.

## Alternative Inference Apps

There are many alternatives to Ollama out there: llama.cpp, vLLM, etc. They all require more work to get the models running, compared to Ollama.

If you must have a very hackable app, try llama.cpp. Ollama is a CLI / GUI built on top of llama.cpp, using sensible default values. llama.cpp will expose all the model config details. But you're on your own when it comes to model downloading, configuring, quantizing, etc.

https://github.com/ggml-org/llama.cpp

And look for models on HuggingFace:

https://huggingface.co/
