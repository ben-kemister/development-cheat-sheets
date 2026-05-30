---
title: "LiteLLM"
tags:
- litelllm
---

[LiteLLM](https://docs.litellm.ai/) is an open-source API gateway that acts as a "universal translator," allowing any 
OpenAI-compatible client to connect to 100+ different LLM backends (like Ollama, vLLM, or Anthropic) through a single 
standardized endpoint.
<!--more-->

## Topic Specific Pages

{{% children sort="title" description="true" %}}

## Deployment

LiteLLM can be deployed to Kubernetes using plain resource manifests or using a Helm chart.
For more information see [Deployment - LiteLLM](https://docs.litellm.ai/docs/proxy/deploy#kubernetes).

## AI Gateway (Proxy)

The proxy configuration values can be found:
* [Config.yaml | Overview](https://docs.litellm.ai/docs/proxy/configs)
* [Config.yaml | All settings](https://docs.litellm.ai/docs/proxy/config_settings)

## Providers

### OpenAI Compatible providers

```yaml
model_list:
  - model_name: gemma4
    litellm_params:
      # Need to prefix the model with the provider name (in this case 'openai')
      model: openai/gemma4
      api_base: http[s]://<HOST_NAME>/
      # This needs to match an Environmental variable (i.e. YOUR_OPENAI_API_KEY)
      api_key: os.environ/YOUR_OPENAI_API_KEY
```

## Endpoints

### Model List

```shell
curl https://<LITELLM_HOST>/models
```
```json
{
  "data": 
  [
    {
      "id":"gemma4",
      "object":"model",
      "created":1677610602,
      "owned_by":"openai"
    },
    {
      "id":"gemma4-E4B",
      "object":"model",
      "created":1677610602,
      "owned_by":"openai"
    }
  ],
  "object":"list"
}
```