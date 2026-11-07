# dsh-config-mistral-vibe

A [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness) (dsh) desktop profile that sends model requests to Mistral's API the way [Mistral Vibe](https://github.com/mistralai/mistral-vibe) does, with the same two models.

Tested with dsh desktop **0.2.2-rc.1** on macOS.

## What it sets up

All the settings are in [`cordis.patch.yml`](cordis.patch.yml). The other files are dsh's defaults for a desktop profile.

| | |
|---|---|
| Provider | dsh's built-in `mistral` provider, shown as "Mistral Vibe". It uses Mistral's own chat API (`https://api.mistral.ai/v1/chat/completions`), the same backend Vibe uses. |
| API key | Read from the dsh credential named `MISTRAL_VIBE_API_KEY`. The key itself is never in the file. |
| GLM-5.3 (`zai-glm-5-3`) | Thinking levels Low / High / Max. It can't be turned off. Text only. |
| DeepSeek V4.1 Flash (`deepseek-v4-1-flash-1017`) | Thinking levels Off / Low / High / Max, the same as DeepSeek's own API. Off sends `reasoning_effort: "none"`, which really turns thinking off. Text and images. |
| Default | DeepSeek V4.1 Flash, thinking on Max. |
| `x-affinity` header | Keeps your requests on the same Mistral server so they reuse its prompt cache. Vibe sends this header too. |

## Use it

You need dsh desktop and a Mistral API key that can use both models above.

1. Quit dsh completely (⌘Q on macOS).
2. Back up your current `~/.dsh/profiles/desktop/cordis.patch.yml` (it's under `$DSH_HOME` if you've set that).
3. Copy this repo's `cordis.patch.yml` into its place.
4. **Replace the `x-affinity` value with your own** (see below):
   ```bash
   uuidgen | tr 'A-Z' 'a-z'
   ```
5. Open dsh and add your Mistral API key on its Models page under the name `MISTRAL_VIBE_API_KEY`. If the key is missing, requests fail with `MISSING_CREDENTIAL`.

## About `x-affinity`

The value is just a random ID. Mistral appears to use it only as a routing key: requests with the same value go to the same server, which still has the earlier part of your conversation cached. It isn't tied to your account and doesn't log you in. Your API key does that.

Copying the value from this repo still works, but then your requests share a server with everyone else who copied it. Generate your own.

Vibe goes further and uses a different ID for each session. dsh can only set fixed headers in this file, so this config uses one ID for every session. That's a close match for one person working in one session at a time.

## Things to know

- **dsh rewrites `cordis.patch.yml`** when you change settings in the app, and it removes comments when it does. Keep your notes here, not in the YAML.
- **Switching back to DeepSeek's own provider:** pick it in dsh's model picker instead of editing `provider:` by hand. The same model has a different ID there (`deepseek-flash`, not `deepseek-v4-1-flash-1017`), so changing only the provider fails with an unknown-model error. You'll also need a DeepSeek key or sign-in, because your Mistral key won't work there. None of the Mistral settings, including `x-affinity`, apply to DeepSeek's provider.
