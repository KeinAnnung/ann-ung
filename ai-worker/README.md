# Ann-Ung · Monigote AI Worker

This Worker is the optional AI backend for the floating Monigote assistant on GitHub Pages.

## Model and limits

- Model: \`@cf/meta/llama-3.2-1b-instruct\`, a small hosted, open-weight instruction model.
- Workers AI currently publishes a free allocation of 10,000 Neurons per day. This is quota, not an uptime or forever-free guarantee. On the Free plan, requests fail after the quota is reached. Check current terms before deployment.
- Output is capped at 180 tokens; message history is capped; user messages are capped at 600 characters.
- The edge rate limiter allows 8 requests per 60 seconds per IP key, per Cloudflare location. This is a lightweight abuse guard, not a globally exact daily quota.
- The Worker does not intentionally persist conversation content or log the submitted message text.

## Deploy

1. Create/sign in to a Cloudflare account and enable Workers AI for the account.
2. Install Node.js and Wrangler on your machine.
3. From the repository root, run:

   \`\`\`bash
   npx wrangler login
   npx wrangler deploy --config ai-worker/wrangler.jsonc
   \`\`\`

4. Cloudflare prints a URL such as \`https://ann-ung-monigote-ai.YOUR-SUBDOMAIN.workers.dev\`.
5. Open the Ann-Ung subpage, open Monigote → **Configurar IA**, and paste the full Worker URL ending in \`/chat\`. The URL is stored only in that browser's local storage.
6. The expected browser Origin is \`https://keinannung.github.io\`. If the project moves to a custom domain, update \`SITE_ORIGIN\` in \`src/index.js\` and redeploy.

Health check: open \`https://YOUR-WORKER.workers.dev/health\` from the allowed site origin. It reports whether the AI binding is available.

## Security and limitations

- Never put a Cloudflare API token, account credential, or model key in the static website. The Worker binding authorizes inference server-side.
- CORS is restricted to the GitHub Pages origin. CORS is not authentication; public APIs can still be called by non-browser clients. The rate limiter, message limits, and model output cap are guardrails.
- Chat text leaves the visitor's browser and is sent to Cloudflare Workers AI when connected. Do not send passwords, authentication codes, private records, or sensitive information.
- The model has no browsing tools and cannot verify live/current facts. Responses can be wrong.
- The G refusal exists in the frontend, Worker, and system prompt, but it is a narrative safeguard, not a cryptographic guarantee against every possible prompt.
