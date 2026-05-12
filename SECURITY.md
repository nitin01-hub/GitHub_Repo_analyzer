Security Policy
Supported Use
This MCP server is designed as a local stdio server for read-only GitHub repository analysis. It does not expose an HTTP endpoint and should not be bound directly to the network.

Secrets
Store GITHUB_TOKEN in the MCP host configuration, shell environment, or secret manager.
Do not commit .env files or tokens.
Use a fine-grained GitHub token with the minimum required access.
Public repositories do not require a token, but unauthenticated requests have low rate limits.
Tool Safety
The current tools only read GitHub API data. If write-capable tools are added later, require explicit user confirmation, scoped authorization, audit logs, and idempotency protections.

Remote Deployment
If this server is adapted to Streamable HTTP, add HTTPS, authentication, origin validation, rate limiting, and MCP authorization/token audience validation before exposing it publicly.
