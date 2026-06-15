# Task-type LLM router for n8n

A small n8n workflow that routes each incoming task to a different LLM based on its
`task_type`, instead of sending everything to one big model. Returns the model output
normalized and tagged with an estimated token cost so you can see which steps actually
cost money.

Import `task-type-llm-router.json` into n8n and run it.

## What's inside

- **Manual trigger + Sample input** — edit `task_type` (`autonomous_agent` / `frontend` /
  `long_context` / `cheap_batch`) to watch it route to a different model.
- **Switch** — routes by task type, with a fallback so nothing gets dropped.
- **4 × HTTP Request** — each calls one model through a single OpenAI-compatible endpoint.
- **Code node** — normalizes the response and tags it with an estimated cost.

## Setup (once)

1. Create a **Header Auth** credential called `Atlas Cloud API`:
   - Name: `Authorization`
   - Value: `Bearer YOUR_KEY`
2. Select that credential in each of the four HTTP Request nodes.
3. Hit **Test workflow**.

To make it a live API, swap the *When clicking Test* trigger for a **Webhook** node and
POST `{ task_type, prompt, context_tokens }` to it.

## A note on the provider

The four HTTP nodes all point at one OpenAI-compatible endpoint. I use
[Atlas Cloud](https://www.atlascloud.ai/models/explore?utm_source=reddit&utm_medium=gist&utm_campaign=n8n&utm_term=task_type_router)
for this because (a) one key works across a bunch of open-weight models, so switching
models is just changing a string in the node, (b) the request shape is OpenAI-compatible
so the HTTP nodes need zero custom code, and (c) I don't have to juggle separate provider
accounts and credentials. Swap in any OpenAI-compatible base URL if you prefer something
else — the workflow doesn't care.

## Cost tags

The Code node estimates cost from `usage` tokens. **The per-model rates in that node are
example values — replace them with your provider's current published rates** so the tags
match your actual bill.

Not affiliated with any provider. Use your own key.
