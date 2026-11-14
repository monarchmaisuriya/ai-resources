# Misuse patterns

## Contents

- Python
- JavaScript and TypeScript
- Config
- Auth
- Outbound requests
- Dependencies
- Kubernetes and Helm
- IAM
- LLM and agent tools

Read the sections that match the code or the design in front of you. Each row is a pattern and what goes wrong. Ideas adapted from Trail of Bits Sharp Edges (https://github.com/trailofbits/skills/tree/main/plugins/sharp-edges).

## Python

| Pattern | What goes wrong |
| --- | --- |
| `def f(x=[])`, `def f(x={})` | One default object is shared by every call; state leaks between requests. |
| `eval`, `exec`, `compile` on input | Runs attacker code. |
| `pickle.loads`, `yaml.load` without a safe loader | Loading the data runs code. |
| `lambda: i` or an inner function created in a loop | Every closure sees the loop variable's last value. |
| `x is 1`, `x is "text"` | Tests identity, not equality; works by accident for small ints and interned strings. |
| bare `except:` or `except Exception:` | Swallows the real error, including one that should have denied access. |
| `class C: items = []` | One list shared by every instance. |
| `user_template.format(obj)` | The template can read attributes of `obj`. |
| `subprocess.run(..., shell=True)` with input | Command injection; pass an argument list with no shell. |
| `a.b.c.d` where any step can be `None` | `AttributeError` far from the cause. |
| `a, b, c = user_list` | Wrong length fails only after reading the whole input. |
| float equality, `float(big_int)` | Precision loss; use integers or `Decimal` for money and ids. |

## JavaScript and TypeScript

| Pattern | What goes wrong |
| --- | --- |
| `==` | Type coercion: `"0" == false` is true. |
| `obj[userKey] = value`, deep merge of input | Prototype pollution through `__proto__`, `constructor`, `prototype`. |
| nested quantifiers such as `(a+)+`, `(.*)+` | Crafted input makes the regex run for a very long time. |
| `parseInt(x)` with no radix | Inconsistent parsing. |
| `eval`, `new Function`, `setTimeout("code")` | Runs attacker code. |
| `nums.sort()` with no comparator | Sorts as strings: `[10, 9, 1]` becomes `[1, 10, 9]`. |
| `value as Admin`, `any` | No runtime check; the type is a claim, not a fact. |
| `name!` | Crashes when the value is null after all. |
| async call with no `await` | The next line runs before the check finishes. |
| promise with no `catch` | An unhandled rejection can crash Node. |
| `JSON.parse` of integers above 2^53 | Silent precision loss in ids. |
| type guard `x is Admin` that returns true too easily | TypeScript trusts it. |

## Config

- `0`, `""`, and `None` never mean "off" or "forever" for a security value. Reject them, or give them a stated safe meaning.
- No flag silently turns a check off (`verify_ssl: false`). If one must exist, it is loud and kept apart from normal settings.
- Double negatives (`disable_no_auth`) get renamed.
- Magic values (`-1` for "unlimited") get a named constant and a bound.
- Contradictory settings fail at load instead of one silently winning.
- The order of precedence between file, environment, and command line is written down.
- An environment variable that can relax a security setting is treated as reachable by an attacker.
- Paths taken from config stay inside the expected directory.
- The string `"false"` is never silently turned into a boolean.
- Security parameters on constructors and functions are validated, not just defaulted: algorithms against an allowlist, time values against a minimum and a maximum, hosts and URLs by format.
- A new key whose name holds `PASSWORD`, `SECRET`, `KEY`, or `TOKEN` in a ConfigMap, Helm values, or a Dockerfile `ENV` is a secret in plain config. Flag it for a check before release: where secrets must live is a team decision.
- CORS never echoes an arbitrary `Origin`, never allows `null`, and never matches origins by substring or suffix. This matters most with `Access-Control-Allow-Credentials: true`.
- Responses send HSTS, a Content-Security-Policy, `X-Content-Type-Options: nosniff`, and a Referrer-Policy; no server or framework version leaks into headers; `DEBUG` is off in production. A page that must load inside another site's frame sets CSP `frame-ancestors` to that site instead of `X-Frame-Options: SAMEORIGIN`. Do not require the obsolete `X-XSS-Protection`.

## Auth

- Secrets are compared in constant time (`hmac.compare_digest`, `crypto.timingSafeEqual`), and empty or `None` never matches.
- Passwords are not truncated before hashing.
- Login errors do not reveal whether the username exists.
- Session ids are issued by the server, never taken from the request.
- Tokens come from a cryptographic random source and are long enough.
- Session and token timeouts have no `0` or negative "never expires" meaning.
- One-time codes: bounded lifetime, each code works once, wrong guesses are limited.
- Reset tokens work once and expire.
- Permissions are typed values (an enum or set), not comma-separated strings, and checks match exactly, never "any of" or by substring.
- The same auth check is present on every sibling route or task, not only one.
- An object id from the request is checked against what the caller owns; sequential ids are not secret.
- Multi-factor auth is enforced on the server, cannot be switched off from the same session, and "remember this device" uses a strong token.
- Recovery codes are random, work once, and are limited.
- OAuth: `state` is generated per request and checked on return; `redirect_uri` matches an exact allowlist, never a prefix; an authorization code works once; public clients use PKCE; the client cannot widen the granted scope; the client secret never reaches the browser.
- Signed tokens (JWT): the accepted algorithm is pinned on the server, never read from the token header and never `none`; signature, `exp`, `nbf`, `aud`, and `iss` are all checked; a `kid` header is never used as a file path or query input.
- Mass assignment: a create or update endpoint binds only an explicit list of fields. Privileged fields (role, owner, tenant, `is_staff`, price) are read-only and set by the server. In Django REST Framework, `fields = "__all__"` on a writable serializer is a finding.

## Outbound requests

- Any server-side fetch of a URL that a user, merchant, or webhook payload can influence (import from URL, image or preview fetch, webhook target) is an SSRF risk. The host is checked against an allowlist, redirects are re-checked, and private, link-local, and cloud metadata addresses (`169.254.169.254`) are refused after DNS resolution.

## Dependencies

- Judge the version actually installed (the lockfile), not the range in the manifest.
- Look up advisories for that exact version (OSV, GitHub advisories) and record the first fixed version.
- Say whether the vulnerable code is reachable from ours. Unreachable lowers severity; it does not remove the finding.
- A known-exploited advisory outranks a higher raw CVSS score.
- A lookup that errors or times out is "not checked", never "no vulnerabilities".

## Kubernetes and Helm

- Review the merged result: chart defaults, the environment values file, and deploy-time `--set`. A template is not the deployed spec.
- Containers run with `runAsNonRoot`, `allowPrivilegeEscalation: false`, and capabilities dropping `ALL`; never `privileged`, `hostNetwork`, `hostPID`, or `hostIPC`. A writable root filesystem needs a stated reason. Check pod-level and container-level `securityContext`, and `initContainers` too.
- Every container sets resource requests and a memory limit.
- Images are pinned by digest or a unique build tag, never `latest` or a reused tag.
- Roles and ClusterRoles have no wildcard verbs or resources; workloads are not bound to `cluster-admin`; `automountServiceAccountToken: false` unless the pod calls the Kubernetes API.
- A `LoadBalancer` Service or ingress states its allowed source ranges. Security-group rules treat protocol `-1` as all traffic and check `::/0` as well as `0.0.0.0/0`.
- Nodes enforce IMDSv2 with a hop limit of 1, so pods cannot borrow the node's AWS role.
- Secrets go in Secrets or an external secret store, not ConfigMaps or values (see Config).

## IAM

- No `"*"` action or resource without a stated reason, including `"s3:*"`, `["*"]`, and `NotAction`.
- `iam:PassRole` and cross-account trust name exact roles and accounts.
- A CI OIDC trust policy pins the repository and branch in its `sub` condition, not a whole organization.
- Workloads use pod identity (IRSA or EKS Pod Identity), not long-lived access keys in the environment.

## LLM and agent tools

- Model output is untrusted input: never pass it unencoded into SQL, a shell, `eval`, a template, or HTML. Validate structured output against a strict schema and reject it when it fails to parse.
- Content the model reads (web pages, emails, tickets, retrieved documents, tool results) can carry instructions. It must not be able to trigger a tool call on its own.
- Each tool has the narrowest scope that does its job: no general shell, no HTTP to arbitrary hosts.
- Tool arguments are validated on the server against an allowlist, because the model wrote them.
- Irreversible or outbound actions (send, pay, delete, deploy, publish) need a human confirmation step.
- Retrieval filters by the caller's permissions, not the app's.
- Persistent agent memory is untrusted when read back: an earlier injection can seed it.
- An MCP server authenticates clients, scopes resources, rate-limits, logs every tool call, and never returns secrets.
- Testing a guardrail: one refusal is not a pass. Retry the same goal with at least three phrasings. A canary string echoed back is proof; a refusal-keyword match is only a hint.
- Token and cost limits apply per caller.
