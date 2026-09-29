# Stack and SDK selection

Use the target application's existing package manager and dependency style.
All official SDKs expose email sends, receiving, domains, audiences/contacts,
segments, topics, campaigns, templates, automations, webhooks, events, logs,
and polls unless their language conventions say otherwise.

| Stack | Install | Minimum | Client |
| --- | --- | --- | --- |
| Node.js / TypeScript | `npm install senddart` | Node 18 | `new SendDart(process.env.SENDDART_API_KEY!)` |
| Python | `pip install senddart` | Python 3.8 | `senddart.api_key = os.environ["SENDDART_API_KEY"]` |
| PHP | `composer require senddart/senddart` | PHP 8.1 | `SendDart::client(getenv('SENDDART_API_KEY'))` |
| Ruby | `gem "senddart"` | Ruby 2.7 | `SendDart.configure { |c| c.api_key = ENV.fetch("SENDDART_API_KEY") }` |
| Go | `go get github.com/shekhu10/senddart-sdks/senddart-go` | Go 1.22 | `senddart.NewClient(os.Getenv("SENDDART_API_KEY"))` |
| Java | Maven `com.senddart:senddart:1.0.0` | Java 11 | `new SendDart(System.getenv("SENDDART_API_KEY"))` |
| .NET | `dotnet add package SendDart` | .NET 8 | `SendDartClient.Create(Environment.GetEnvironmentVariable("SENDDART_API_KEY")!)` |
| Rust | `cargo add senddart` | Rust 1.75 | `SendDart::new(std::env::var("SENDDART_API_KEY")?)` |

Prefer a long-lived client per process and inject it into the application's mail
service. In serverless runtimes, module-scoped initialization is appropriate as
long as the key is read server-side and tests can replace the transport.

## Node / Next.js

Use a server-only module. In Next.js, add `import 'server-only'` when available
and call the service from Route Handlers, Server Actions, or background jobs.
Never import it into a client component.

```ts
import 'server-only';
import { SendDart } from 'senddart';

const apiKey = process.env.SENDDART_API_KEY;
if (!apiKey) throw new Error('SENDDART_API_KEY is not configured');

export const senddart = new SendDart(apiKey);
```

Keep actual product methods above this low-level client and pass a stable
operation key:

```ts
await senddart.emails.send(payload, { idempotencyKey: `welcome:${user.id}:v1` });
```

## Python

Initialize once from the environment, wrap calls behind the application's
service layer, and catch `senddart.SendDartError`. Branch on `e.name` and
read `e.status_code`; do not parse `str(e)`.

## PHP / Ruby

Register the client/service with the framework container or initializer. Do not
resolve secrets inside templates, jobs, or controllers. Catch the SDK's typed
exception and preserve structured fields for retry decisions.

## Go / Java / .NET / Rust

Reuse the client and underlying HTTP connection pool. Thread application
context/cancellation through calls where the SDK supports it. Map SDK errors
into the application's typed error/retry model at the mail boundary.

## REST fallback

For another server runtime, use its standard pooled HTTP client:

```text
POST https://www.senddart.com/api/emails
Authorization: Bearer <server-only key>
Content-Type: application/json
User-Agent: <application/version>
Idempotency-Key: <stable logical operation id>
```

Centralize auth, timeouts, JSON parsing, structured errors, and bounded retry
logic. Do not replicate raw HTTP calls across business features.
