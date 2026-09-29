# SendDart AI Setup Skill

An installable coding-agent skill that sets up SendDart safely in an existing
application. It detects the host stack, guides the user through dashboard-only
API-key and verified-domain setup, installs the correct official SDK or REST
client, builds an application-owned mail service, and verifies transactional
email, campaigns, signed webhooks, or inbound mail as required.

## Install

```bash
npx skills add shekhu10/senddart-ai-skill
```

Then ask your coding agent:

```text
Use $senddart-ai-skill to set up SendDart securely in this codebase and verify the integration end to end.
```

The skill never creates or prints API keys. Key lifecycle stays in the
[SendDart API Keys dashboard](https://www.senddart.com/app/api-keys), and
real credentials stay in your application's secret manager.

## What it covers

- Node.js/TypeScript, Python, PHP, Ruby, Go, Java, .NET, Rust, and REST
- server-only client and configuration boundaries
- verified sending domains and safe simulator testing
- stable idempotency and bounded retry/recovery
- transactional and consent-aware marketing architecture
- signed webhook verification and durable deduplication
- inbound email handling and attachment/content safety
- deterministic project detection and setup verification

## Local validation

```bash
python3 scripts/detect_project.py --root /path/to/project
python3 scripts/verify_setup.py --root /path/to/project
python3 -m unittest discover -s tests -v
```

See [SendDart documentation](https://www.senddart.com/docs) for the full API
and product reference.
