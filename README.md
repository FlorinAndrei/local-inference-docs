# Local Inference for Generative AI

If you use generative AI for coding and other purposes, then frontier models (Claude Opus, etc) are the best. But they are not free.

If you ever run out of tokens with the frontier models, and are looking for "free" alternatives, one option is to run inference locally. These documents describe how to do that.

Assumptions:

- Your daily driver system is something like a MacBook Pro or similar (typical for coders), or a fairly powerful gaming laptop or PC. You may or may not have extra hardware available.
- You're a coder, although anybody who wishes to do local inference will find most information here useful (except for some coding-specific client apps).

It's not an easy thing to do well.

## Hardware

Read this part at least once, no matter how well you feel you understand hardware. There are hardware challenges specific to inference that you should know.

[hardware.md](hardware.md)

## Models and Inference Apps

This is the inference engine. This is how you generate tokens from prompts.

[models.md](models.md)

## Model Tuning

Change the context window size and other parameters.

[tuning.md](tuning.md)

## Client Apps

- coding apps such as OpenCode (similar to the Claude Code CLI, but free)
- Web interfaces such as Open WebUI (similar to the Claude Web interface)

[clients.md](clients.md)

## Performance

How well does this work?

In terms of speed, with the right hardware it's about as fast as Claude.

In terms of intelligence, open-weights models are less than 1 year behind frontier models. 9 months perhaps? Estimates vary.

## Security

Ollama is just an API, the models don't "do" anything within it.

It's up to the controls within OpenCode and Open WebUI to not let agents run amok. They are quite similar to Claude Code and Claude Web, and you should use the same common sense when using them. Do not enable risky tools. If you're comfortable with Claude Code, then you know what to do, and what not to do, with OpenCode.

The Ollama API endpoint created here is not secured at all, it's plain HTTP and passwordless. This is a home network setup. If that's not okay, feel free to put a password on it, TLS, reverse proxy, etc. Or at least bind it to localhost only. That's out of scope for this document.

The Open WebUI endpoint created here has a password, but it's plain HTTP. You could add TLS to it. That's out of scope for this document.
