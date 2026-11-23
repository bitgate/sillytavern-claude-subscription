# SillyTavern with a Claude subscription

Yes, SillyTavern runs on your Claude Pro or Max subscription: add [Plan2API](https://plan2api.com/integrations/sillytavern) as a **Custom (OpenAI-compatible)** Chat Completion source at `https://api.plan2api.com/v1` with your `p2a_` key. Pick `claude/claude-sonnet-5` and every swipe runs on the plan you already pay for, with no Anthropic API key, no reverse proxy to host and no per-token bill.

The same key runs ChatGPT Plus or Pro (`openai/gpt-6.1-sol`), Gemini on Google AI Pro (`gemini/gemini-3.8-flash-high`) and Kimi Code (`kimi/k3`), so one SillyTavern connection covers every AI subscription you own.

**[Get a free `p2a_` key](https://plan2api.com/connect?utm_source=github&utm_medium=readme&utm_campaign=sillytavern-claude-subscription)** | [SillyTavern guide on plan2api.com](https://plan2api.com/integrations/sillytavern) | [API reference](https://plan2api.com/docs)

## Settings at a glance

| SillyTavern field | Value |
| --- | --- |
| API | `Chat Completion` |
| Chat Completion Source | `Custom (OpenAI-compatible)` |
| Custom Endpoint (Base URL) | `https://api.plan2api.com/v1` |
| Custom API Key | your `p2a_...` key |
| Model | `claude/claude-sonnet-5` (from **Available Models**, or typed into **Enter a Model ID**) |
| Context Size (tokens) | `1000000` for Claude Sonnet 5 and Opus 5.5 |
| Max Response Length | up to `128000` on Claude |
| Prompt Post-Processing | `None` (or `Semi-strict`, see below) |

## Setup in 3 minutes

### 1. Turn your Claude subscription into an API key

1. Open [plan2api.com/connect](https://plan2api.com/connect?utm_source=github&utm_medium=readme&utm_campaign=sillytavern-claude-subscription) and sign up with Google.
2. Connect Claude by signing in with your Claude account on Anthropic's own sign-in page. Pro, Max 5x and Max 20x all work.
3. Copy the `p2a_` key you get after connecting. It is shown once; create more any time on the Keys page (`plan2api.com/dashboard/keys`).

Want GPT as well? Connect ChatGPT with a one-time device code: open the link Plan2API shows you, sign in on OpenAI's own page and enter the code. Device code login has to be switched on in your ChatGPT security settings.

### 2. Point SillyTavern at Plan2API

1. Open the **API Connections** panel (the plug icon in the top bar).
2. Set **API** to `Chat Completion`.
3. Set **Chat Completion Source** to `Custom (OpenAI-compatible)`.
4. **Custom Endpoint (Base URL)**: `https://api.plan2api.com/v1`. Stop at `/v1`, because SillyTavern appends `/chat/completions` itself.
5. **Custom API Key**: paste your `p2a_` key.
6. Click **Connect**. **Available Models** now lists every Plan2API model: pick `claude/claude-sonnet-5`, or type it into **Enter a Model ID**.
7. Hit **Test Message**. A reply means you are live on your Claude plan.

Save the setup as a Connection Profile so you can flip between Claude and ChatGPT in one click.

### 3. Tune the response settings

- **Context Size (tokens)**: the Custom source goes up to 2,000,000, so match it to the model: `1000000` for `claude/claude-sonnet-5` and `claude/claude-opus-5-5`, `200000` for `claude/claude-haiku-4-5` and `claude/claude-sonnet-4-6`, `1048576` for `gemini/gemini-3.8-flash-high` and `kimi/k3`.
- **Max Response Length**: Claude models take up to `128000` output tokens (`64000` on `claude/claude-haiku-4-5`).
- **Samplers**: Gemini uses `temperature`, `top_p` and stop strings. Claude and Codex models ignore all three, Kimi pins `temperature` at 1 and `top_p` at 0.95 and ignores stop strings, and `frequency_penalty`, `presence_penalty` and `top_k` are ignored on every model.
- **Prompt Post-Processing**: start with `None`. If you inject an Author's Note or World Info at a chat depth with the `system` role, switch to `Semi-strict` so those mid-chat injections arrive as `user` messages instead of being pulled up into Claude's system prompt.

### 4. Turn on Claude's thinking

On the Custom source, SillyTavern's **Reasoning Effort** dropdown sends nothing for Plan2API model ids, so put the effort in the request body:

1. In **API Connections**, click **Additional Parameters**.
2. Under **Include Body Parameters**, paste the YAML from [`include-body-parameters.yaml`](include-body-parameters.yaml):

```yaml
reasoning_effort: high
```

Claude and Gemini accept `none`, `minimal`, `low`, `medium`, `high`, `xhigh` and `max`. Kimi maps the value to `low`, `high` or `max`, and Codex passes it straight through to ChatGPT.

## SillyTavern with ChatGPT Plus

Connect ChatGPT in Plan2API, keep the exact same SillyTavern connection and switch the model to `openai/gpt-6.1-sol`. GPT now runs on your ChatGPT Plus or Pro plan's Codex allowance instead of an OpenAI API key. Plan2API reads the Codex model list live from OpenAI, so `openai/gpt-6-astra`, `openai/gpt-6.1-terra` and `openai/gpt-6-luna` work too: pick one your plan includes.

## Models

| Model id | Runs on | Context window |
| --- | --- | --- |
| `claude/claude-sonnet-5` | Claude Pro or Max | 1,000,000 |
| `claude/claude-opus-5-5` | Claude Pro or Max | 1,000,000 |
| `claude/claude-sonnet-4-6` | Claude Pro or Max | 200,000 |
| `claude/claude-haiku-4-5` | Claude Pro or Max | 200,000 |
| `openai/gpt-6.1-sol` | ChatGPT Plus or Pro (Codex) | listed by `GET /v1/models` |
| `gemini/gemini-3.8-flash-high` | Google AI Pro or Ultra | 1,048,576 |
| `kimi/k3` | Kimi Code membership | 1,048,576 (starts on 256K, moves to 1M past about 192K) |

`anthropic/` works as an alias for `claude/`, and dotted names such as `claude/claude-opus-5.5` resolve too. Always keep the prefix, because an id without one goes to Codex. The full list (14 Claude ids, 5 Gemini ids and the live Codex ids) is in the [API reference](https://plan2api.com/docs).

## Check your key outside SillyTavern

```bash
curl https://api.plan2api.com/v1/chat/completions \
  -H "Authorization: Bearer $PLAN2API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"model": "claude/claude-sonnet-5", "messages": [{"role": "user", "content": "Say hi to SillyTavern."}]}'
```

A JSON reply with `choices` means the key and model are good, and anything still failing is in the SillyTavern settings.

## Troubleshooting

| What you see | Why | Fix |
| --- | --- | --- |
| `404` `not_found` | The endpoint ends in `/chat/completions` or is missing `/v1` | Use exactly `https://api.plan2api.com/v1` |
| `401` `invalid_api_key` | Key missing, mistyped or revoked | Paste the `p2a_` key into **Custom API Key** again, or create a new one on the Keys page |
| `403` `no_credentials` | The model belongs to a provider you haven't connected (the model list shows every provider) | Connect that subscription at plan2api.com, or pick a `claude/` model |
| `400` `model_not_found` | Typo or missing prefix | Copy the id exactly, for example `claude/claude-sonnet-5` |
| `400` `context_length_exceeded` | The prompt is longer than the model's window | Lower **Context Size (tokens)** to the window in the models table |
| `429` `subscription_quota_exhausted` | Your Claude plan hit its 5-hour or weekly limit | Wait for the `Retry-After` time, or connect a second Claude account so requests fail over automatically |
| `429` `rate` or `inflight` | Over your Plan2API request rate (Free: 1 per second, bursts of 20, 4 at once) | Ease off group chats and auto-continue, or move to Pro (10 per second, 16 at once) |
| Temperature changes nothing | Claude and Codex ignore `temperature` and `top_p` | Steer with `reasoning_effort` and your prompt, or use a Gemini model |
| No thinking at all | The Reasoning Effort dropdown sends nothing on the Custom source | Add `reasoning_effort: high` under **Include Body Parameters** |

## FAQ

### Can I use my Claude Pro subscription in SillyTavern?

Yes. SillyTavern's built-in Claude source takes a pay-per-token Anthropic Console API key, so connect your Claude Pro or Max plan to Plan2API and add it as a Custom (OpenAI-compatible) source instead. Every chat then runs on your subscription's usage limits.

### Does SillyTavern support Claude subscription OAuth login?

Plan2API handles it: you sign in with Claude on Anthropic's own page once, and SillyTavern gets a normal OpenAI-style `p2a_` key, which is all its Custom source needs.

### Is there a SillyTavern Claude proxy that runs on a monthly subscription?

That's exactly what Plan2API is: a hosted OpenAI-compatible reverse proxy in front of your own Claude subscription. There is nothing to self-host, no per-token charge and no daily request cap. Your Claude plan's 5-hour and weekly limits are the only ceiling, and connecting more accounts raises it.

### Can I use ChatGPT Plus in SillyTavern?

Yes. Connect ChatGPT in Plan2API with a one-time device code, keep the same SillyTavern connection and pick `openai/gpt-6.1-sol`.

### Do I need an Anthropic API key or OpenRouter credits?

No. The only key involved is your `p2a_` key, and requests run on the subscription you connected.

### Does streaming work?

Yes. Plan2API streams server-sent events, so SillyTavern's **Streaming** toggle works exactly as it does with OpenAI.

### Does it work with Claude Max?

Yes. Pro, Max 5x and Max 20x all connect the same way; Max just gives you more room before `429 subscription_quota_exhausted`.

### What does it cost?

Plan2API Free is $0 with one connected subscription and no card. Pro is $12 a month for 3 subscriptions and higher rate limits, and Scale is $29 a month for 25. See [pricing](https://plan2api.com/pricing).

## More guides

- [SillyTavern + Plan2API guide](https://plan2api.com/integrations/sillytavern)
- [SillyTavern with a Claude subscription](https://plan2api.com/sillytavern-claude-subscription)
- [SillyTavern with ChatGPT Plus](https://plan2api.com/sillytavern-chatgpt-plus)
- [Use your Claude subscription as an API](https://plan2api.com/claude-subscription-api)
- [Claude Pro and Max usage limits](https://plan2api.com/claude-usage-limits)
- [Plan2API API reference](https://plan2api.com/docs)

Same setup, other clients:

- [Janitor AI Claude proxy](https://github.com/bitgate/janitor-ai-claude-proxy)
- [Open WebUI with a ChatGPT subscription](https://github.com/bitgate/open-webui-chatgpt-subscription)
- [Cline, Roo Code and Kilo Code with a Claude subscription](https://github.com/bitgate/cline-claude-subscription)
- [OpenCode with a Claude Pro or Max subscription](https://github.com/bitgate/opencode-claude-subscription)
