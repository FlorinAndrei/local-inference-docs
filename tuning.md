# Context Window Size

LLMs are stateless, and have only one flat input: the context window.

Anything that does not exist in the context window right now, the models will ignore. This is not only for prompts, but literally for anything the models could use (memory files, skills, MCPs).

The bigger the context window size, the more data the models could "consider" at once. Bigger is better.

As you increase the context window size, memory usage will also increase. The relationship is not simple.

The context window size is measured in tokens. Your input is converted to tokens (numbers) before it's shown to the LLM. For English text, 1 token is approximately 4 characters or 0.75 words (varies a lot).

## Check Running Models in Ollama

Models have a maximum context window size, but they could run with smaller windows, which saves RAM. Ollama has rules for choosing a context size automatically, based on how much RAM you have. You may get as little as 4k, or all the way up to the maximum size (usually 128k or 256k or similar).

While a model is running in Ollama, you can check its parameters like this:

```console
$ ollama ps
NAME           ID              SIZE     PROCESSOR    CONTEXT    UNTIL              
gpt-oss:20b    17052f91a42e    14 GB    100% GPU     32768      4 minutes from now
```

That's a context size of 32k, uses 14 GB of memory, and it runs entirely in VRAM.

```console
$ ollama ps
NAME                         ID              SIZE     PROCESSOR          CONTEXT    UNTIL              
glm-4.7-flash-198k:latest    193f897bc5e4    41 GB    41%/59% CPU/GPU    202752     4 minutes from now
```

That's a context size of 198k, memory usage of 41 GB, split between VRAM and system RAM (the machine has 24 GB VRAM). If the model is a MoE (which this one is), it may still run semi-fast this way; if it's a dense LLM, then it will be slow because of offloading to system RAM.

Ollama will try to load as much as possible in VRAM. The parts that don't fit are offloaded to system RAM. MoE LLMs are fine this way, up to a point.

Unified RAM is all the same. The model either fits in RAM, or doesn't. There's no "CPU offloading".

## Setting a Context Window Size in Ollama

To run models with plenty of tools, you want the biggest context window you can get. In the Ollama models repo https://ollama.com/search click the name of the model and see the Context column - that's the biggest context size possible for that model. The Size column is the size of model files.

You can create new model definitions in Ollama, with fixed context sizes, and they will run at the context size you declare, no matter what. This does not duplicate the model weights files (you still need the base model), just creates a new definition. Here are some examples that force the maximum size for coding models.

If Ollama is running in Docker, you may have to run `docker exec -it ollama /bin/bash` first, and then create the definitions (`Modelfile` is a file path, Ollama can't see it if it's not in the container):

glm-4.7-flash, 198k context:

```bash
cat > Modelfile <<EOF
FROM glm-4.7-flash:latest
PARAMETER num_ctx 202752
EOF
ollama create glm-4.7-flash-198k:latest -f Modelfile
rm Modelfile
```

gpt-oss-20b, 128k context:

```bash
cat > Modelfile <<EOF
FROM gpt-oss:20b
PARAMETER num_ctx 131072
EOF
ollama create gpt-oss-20b-128k:latest -f Modelfile
rm Modelfile
```

gpt-oss-120b, 128k context:

```bash
cat > Modelfile <<EOF
FROM gpt-oss:120b
PARAMETER num_ctx 131072
EOF
ollama create gpt-oss-120b-128k:latest -f Modelfile
rm Modelfile
```

qwen3-coder-next, 256k context:

```bash
cat > Modelfile <<EOF
FROM qwen3-coder-next:latest
PARAMETER num_ctx 262144
EOF
ollama create qwen3-coder-next-256k:latest -f Modelfile
rm Modelfile
```

Now run `ollama ls` and you will see the new definitions listed there, along with the base models. Load the models from the new definitions to force the context size you want.

If the models don't fit in VRAM, then running them at a high speed with a large context size may not be possible, especially if they are dense LLMs. MoE LLMs may do well when partially offloaded to system RAM.

TODO: Explain temperature and other parameters.
