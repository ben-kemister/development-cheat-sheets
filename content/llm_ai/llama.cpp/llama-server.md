---
title: "llama.cpp HTTP Server"
tags:
- llm
- llama.cpp
---

The [LLaMA.cpp HTTP Server](https://github.com/ggml-org/llama.cpp/blob/master/tools/server/README.md) is a fast, 
lightweight, pure C/C++ HTTP server based on httplib, nlohmann::json and llama.cpp.
<!--more-->
It provides a set of LLM REST APIs and a web UI to interact with llama.cpp.

## Features

* LLM inference of F16 and quantized models on GPU and CPU
* OpenAI API compatible chat completions, responses, and embeddings routes
* Anthropic Messages API compatible chat completions
* Reranking endpoint (#9510)
* Parallel decoding with multi-user support
* Continuous batching
* Multimodal (documentation) / with OpenAI-compatible API support
* Monitoring endpoints
* Schema-constrained JSON response format
* Prefilling of assistant messages similar to the Claude API
* Function calling / tool use for ~any model
* Speculative decoding
* Easy-to-use web UI


## Serving a model

To serve a model already on the file system use: `llama-server -m "<PATH_TO_MODEL_FILE>"`

For example:
```powershell
.\llama-server.exe -m "D:\models\lmstudio-community\gemma-4-E4B-it-GGUF\gemma-4-E4B-it-Q4_K_M.gguf"
```

This will expose:
* A (chat) web interface at: http://127.0.0.1:8080/

### Arguments

`llama-server` has soooooo many options al of which can be found in the projects [README.md](https://github.com/ggml-org/llama.cpp/blob/master/tools/server/README.md).

Below are some of the common/handy ones I have come across:

| Argument           | Explanation                                                    | 
|--------------------|----------------------------------------------------------------|
| `-c, --ctx-size N` | size of the prompt context (default: 0, 0 = loaded from model) |


### Test that model is working

You can test if the `llama-server` and the model is working correctly with:
```shell
curl --request POST \
    --url http://localhost:8080/completion \
    --header "Content-Type: application/json" \
    --data '{"prompt": "Building a website can be done in 10 simple steps:","n_predict": 128}'
```

Or using PowerShell:
```powershell
$body = @{
    prompt = "Write a short bedtime story about a unicorn."
} | ConvertTo-Json; `
Invoke-RestMethod -Uri "http://localhost:8080/completion" `
                  -Method Post `
                  -ContentType "application/json" `
                  -Body $body
```

## Exposed URLs

Below is a list of some handy URLs exposed by `llama-server`:
* Web () interface at: http://127.0.0.1:8080/
* A health endpoint at: http://127.0.0.1:8080/v1/health
* Completions endpoint at:
  * (not OAI-compatible) http://127.0.0.1:8080/completions
  * (OAI-compatible) http://127.0.0.1:8080/v1/v1/completions
