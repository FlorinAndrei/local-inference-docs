# Client Apps

## OpenCode

A coding tool similar to the Claude Code CLI. It can use many inference back-ends; here we focus on using Ollama.

https://opencode.ai/

### Configure OpenCode

The main configuration file is `~/.config/opencode/opencode.json`. Declare your Ollama endpoints in it, along with the models you want to use from each:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "ollama-local": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Ollama (local)",
      "options": { "baseURL": "http://localhost:11434/v1" },
      "models": { "gpt-oss-20b-128k:latest": { "name": "GPT-OSS 20B 128K" } }
    },
    "ollama-spark": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Ollama (spark)",
      "options": { "baseURL": "http://spark:11434/v1" },
      "models": {
        "glm-4.7-flash:latest": { "name": "GLM 4.7 Flash 198K" },
        "gpt-oss:120b": { "name": "GPT-OSS 120B 128K" },
        "qwen3-coder-next:latest": { "name": "Qwen3 Coder Next 256K" }
      }
    }
  }
}
```

`localhost` is a daily-driver machine, a PC with an RTX 3090, 24 GB of VRAM, so it can only run the smallest coding models. Ollama will not assign models a large context by itself, so you have to force the context size with a custom model definition.

`spark` is a dedicated inference machine with 128 GB of unified RAM, so it can run fairly big open-weights coding models. Because the machine has so much RAM, Ollama loads all models with the context window maxed out, so you don't need to create new definitions, just use the base models.

### Make OpenCode More Like Claude Code

You use Claude Code already. OpenCode is the backup. You want to make sure OpenCode uses as much as possible from the Claude Code tools. Here's a recipe:

#### Memory Files

At the **repository level**, OpenCode supports both the `AGENTS.md` and the `CLAUDE.md` memory files. It's a good idea to create `CLAUDE.md` for Claude, and then create `AGENTS.md` as a symlink to it for all other agents. OpenCode will use whichever it finds first.

In terms of **repository rules**, OpenCode does not look for memory files in `.claude/rules`, but it does have `.opencode/rules`. The formats are similar but not identical. You could try to symlink the latter to the former, and then enable the OpenCode rules plugin in the main config file and see what happens:

```json
{
  "plugin": ["opencode-rules@latest"],
}
```

At the **user level**, Claude uses `~/.claude/CLAUDE.md`, while OpenCode uses `~/.config/opencode/AGENTS.md` memories. However, if the latter does not exist, OpenCode will read the former. You could symlink them, just in case.

TODO: Test this part with complex rules.

#### Skills

Good news: OpenCode supports Claude skills at all levels.

At the **repository level**, `.claude/skills/` are supported.

At the **user level**, `~/.claude/skills/` are also supported.

But you should not assume all Claude-centric YAML frontmatter fields in a `SKILL.md` file are supported.

TODO: Test this part with complex skills, see which fields fail.

#### MCPs

If you run MCPs from Docker Desktop, OpenCode is a supported client, so enable it in Docker Desktop. The following config block will then appear in `~/.config/opencode/opencode.json`:

```json
{
  "mcp": {
    "MCP_DOCKER": {
      "type": "local",
      "command": ["docker", "mcp", "gateway", "run"],
      "enabled": true
    }
  }
}
```

If OpenCode is running in WSL2 but Docker Desktop runs on the host in Windows 11, then replace `docker` with `docker.exe` in the `command` section.

Other MCPs - declare them in the `mcp` block as well. This is similar to `mcpServers` in `~/.claude.json` but not quite identical - some fields are different.

TODO: Expand the example, show more MCPs.

## Open WebUI

This is a Web interface similar to Claude Web.

https://github.com/open-webui/open-webui

Run it wherever you like: on your daily driver machine, on a dedicated inference machine, etc. You will access it with your browser.

It's best to run it in Docker. Create an executable script called `~/bin/update-open-webui.sh` like this:

```bash
#!/usr/bin/env bash

docker stop open-webui
docker rm open-webui
docker pull ghcr.io/open-webui/open-webui:main

# host.docker.internal is the actual host system (optional)
docker run -d --restart always \
	-p 3000:8080 \
	--add-host=host.docker.internal:host-gateway \
	-v open-webui:/app/backend/data \
	--name open-webui \
	ghcr.io/open-webui/open-webui:main
```

Run it once in a while, it will update and restart the app. The container is persistent.

The app will be accessible on port 3000. If it runs on your laptop, access it like this: http://localhost:3000/

### Configure Open WebUI for Ollama

Log in to Open WebUI. Go to: your account name / Admin Panel / Settings / Connections / Ollama API.

Add any Ollama endpoint you may have. If Ollama runs in Docker on the same machine on the default bridge network as shown in [models.md](models.md) then it's bound to the host ports, so the Ollama API URL is:

http://host.docker.internal:11434

If Ollama runs on another machine, just use that machine's hostname or IP address:

http://some.other.machine:11434

You can add multiple Ollama endpoints, and all models will appear in the same place.
