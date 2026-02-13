# Hardware

The requirements can be summarized in one phrase:

You need a large amount of fast RAM, with a mediocre GPU chip plugged into it.

That's it. Now here are some specific details.

## Memory: System RAM vs VRAM vs Unified RAM

Just to make sure we're all on the same page, if your PC has an NVIDIA GPU, then you have two kinds of memory:

- system RAM, used by the CPU
- VRAM, used by the GPU

If you have a MacBook, then you only have unified RAM, shared by the CPU and the GPU.

Transferring data between system RAM and VRAM takes time. With unified RAM, all data is in the same place.

VRAM tends to have a lot of bandwidth. System RAM and unified RAM may or may not have lots of bandwidth.

Your models may run in any combination of the above types of RAM.

## Criteria for Performance

What matters most for inference performance, in terms of hardware? These are the main factors, in decreasing order of importance:

1. Size of VRAM / unified RAM
2. (V)RAM bandwidth
3. Compute power

"Performance" means speed but also quality.

### Speed

Inference speed is measured in tokens / second. More is better. For inference, the bottleneck is (V)RAM bandwidth. If you use dense LLMs, inference speed is completely predictable, the formula is:

$$\text{speed} = \frac{\text{memory bandwidth}}{\text{model size in memory}}$$

E.g. if your memory bandwidth is 273 GB/sec, and the dense LLM uses 21 GB of memory, then you get 13 tokens / second. That means the whole LLM is "slurped" once for each token.

If you use MoE LLMs (mixture-of-experts), the formula is the same, but only a shard of the model is active at any time, and its size is model-dependent and tricky to figure out (you can reverse-engineer the shard size if you know your RAM bandwidth and you measure the speed).

Compute power is least important because inference is bandwidth-intensive, not compute-intensive. Model training is compute-intensive. For inference, relatively weak GPUs are fine, but only if they have lots of VRAM bandwidth.

### Quality

Bigger models tend to be smarter. This is why you need a lot of RAM. There are some very big open-weights models out there. The only limit is your bank account.

## Machines for Inference

There are three main kinds of machines that could run inference.

### Daily Driver, Unified RAM

MacBook Pro with at least 36 GB of unified RAM. If you have less than 36 GB, you will have to turn off apps to run inference. This is a bare minimum.

Both dense LLMs and MoE LLMs work well.

### Daily Driver, NVIDIA GPU

A PC with an RTX 3090 / 4090 / 5090 desktop GPU. They have, respectively, 24 / 24 / 32 GB of VRAM. It's pointless to even try lesser GPUs. You can find second-hand RTX 3090 GPUs out there, they work very well.

These are faster than a MacBook Pro, but only if the model fits in VRAM, which can be tricky. Large MoE LLMs will get their active shard loaded in VRAM, with the rest sitting in system RAM. This works well up to a point; if the active shard is too big, it will get quite slow.

### Dedicated Inference Machine

Lots and lots of fast VRAM / unified RAM. At 96 GB you can do a lot, but ideally you should aim for 128 GB. If your MacBook Pro has this much unified RAM, then it graduates into this category.

Here are some options, the most desirable are shown first:

#### miniPC With AMD Max+ 395

Unified memory, decent GPU, 256 GB/sec memory bandwidth (not huge, but okay). Low power draw. Get all the RAM you can, ideally 128 GB. Run Linux.

#### Mac Mini or Mac Studio

Unified memory, okay GPU. Get all the RAM you can, ideally 128 GB. A Mac Studio may have excellent memory bandwidth, and this the only combo that fully embraces the definition: "a large amount of fast RAM, with a mediocre GPU chip plugged into it".

But Macs are expensive. Look for second-hand options.

#### NVIDIA DGX Spark

128 GB of unified memory, 273 GB/sec memory bandwidth, RTX 5070 equivalent, 20 ARM cores, runs Ubuntu. Low power draw. It's a Blackwell development machine with CUDA, so it's expensive. But it will run inference about as well as an AMD 395.

#### PC with an NVIDIA RTX PRO 6000 GPU

96 GB of VRAM. 1.8 TB/sec of memory bandwidth. Fastest option (it's not the GPU chip, it's the bandwidth). Very expensive.
