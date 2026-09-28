# ✳ Pollinations for Photopea

A Photopea plug-in that generates and edits images with [Pollinations](https://pollinations.ai)
inside a Photopea side panel — **paying with your own Pollen**.

- Generate: type a prompt → the image lands as a **new layer** in the open document.
- Edit: pick a model that accepts image input → the selected layer (or the whole
  document) is edited, and the result lands on a **new layer**. The source layer is
  never modified.
- Models come from the **live catalog** ([`/image/models`](https://gen.pollinations.ai/image/models)),
  community models included — new models appear without a plug-in update.
- Sign in once through Pollinations OAuth (legacy fragment flow, popup + postMessage).
  No API keys are ever pasted. The connection persists in your browser and you can
  disconnect at any time.

## Open it

**App URL (opens Photopea with the plug-in already loaded):**

> https://www.photopea.com/?pl=https%3A%2F%2Fguest453.github.io%2Fpollinations-photopea%2Fplugin.html

Or add it to any Photopea via **Window → Plugins → Add Plugin** with this JSON:

```json
{"name":"Pollinations","url":"https://guest453.github.io/pollinations-photopea/plugin.html","icon":"https://guest453.github.io/pollinations-photopea/icon.svg"}
```

## Demo

![Demo: connecting, generating, editing — result lands on a new layer](demo/demo.gif)

## Install / run locally

No build step. Serve the folder statically (`python3 -m http.server`) or push to a
static host — GitHub Pages serves this repo's root.

### ⚠️ Owner setup (required for sign-in): register the Redirect URI

The App Key used by this plug-in (`pk_5drKIx9HHnvmdcqW`) must have this exact
**Redirect URI** registered in its allowlist:

```
https://guest453.github.io/pollinations-photopea/oauth.html
```

Add it at [enter.pollinations.ai/keys](https://enter.pollinations.ai/keys) → edit the
App Key → **Redirect URIs**. Until it is registered, Pollinations rejects the OAuth
redirect; the plug-in shows a clear message and surfaces this same URI in its
"Owner setup" section. (Anyone hosting a fork must register their own key + URI and
update `CLIENT_ID` / `REDIRECT_URI` in `plugin.html`.)

## How it works

- `plugin.html` — the whole plug-in: one static page. It talks to Photopea via
  [live messaging](https://www.photopea.com/api/live) (`postMessage` scripts,
  `saveToOE` for exporting the layer, ArrayBuffers for handing image data back),
  pulls the model list from `GET https://gen.pollinations.ai/image/models`, and
  calls `/v1/images/generations` / `/v1/images/edits` with the user's own token.
- `oauth.html` — OAuth popup callback (legacy fragment flow): receives
  `#api_key=…`, hands it to the plug-in via `postMessage`, then closes.
- `plugin.json` — the JSON for Photopea's public plug-in gallery.

## Error handling

- **Expired / revoked sign-in** → the panel says the sign-in expired and shows the
  Connect button again.
- **Not enough Pollen** → the panel says to top up at enter.pollinations.ai.
- **Network errors** → the panel says to check the connection and retry.

Out of scope by design: batch generation, history, video.
