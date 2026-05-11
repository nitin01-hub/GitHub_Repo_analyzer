GitHub Repo Analyzer MCP Server
A local Model Context Protocol (MCP) server that exposes read-only GitHub repository analysis tools over stdio. It can be connected to Claude Desktop, Cursor, VS Code, or any MCP-compatible host that supports local servers.

Tools
Tool	Purpose
analyze_repo_structure	File counts, directory overview, file-type distribution, and largest files.
get_repo_insights	Language mix, complexity, tests, docs, CI, and Docker signals.
get_license_details	License detection, SPDX ID, and practical usage implications.
detect_libraries_and_dependencies	Dependency and major framework detection across common ecosystems.
security_check	Basic repository safety heuristics and risk rating.
explain_neural_network_if_present	ML/NN framework and architecture pattern detection.
All tools accept one argument:

{
  "repo": "owner/repo"
}
Production Hardening Included
Strict owner/repo input validation.
GitHub API timeout configuration.
GitHub User-Agent header.
Safe MCP error responses that avoid leaking stack traces, tokens, or internal details.
stderr-only server logging so stdio JSON-RPC is not corrupted.
Malformed remote package.json handling.
Truncated GitHub tree detection to avoid misleading partial analysis.
Strict TypeScript build.
.gitignore, .dockerignore, .env.example, and Docker packaging.
Requirements
Node.js >=18.14.1
npm
Optional GitHub token for higher rate limits or private repositories.
Setup
npm ci
cp .env.example .env
npm run build
For public repositories, GITHUB_TOKEN is optional. For production use or private repositories, set a fine-grained token:

export GITHUB_TOKEN=github_pat_your_token_here
For private repositories, grant read-only repository Contents access.

Run Locally
npm start
The server runs on stdio. It will not print normal logs to stdout because that channel is reserved for MCP protocol messages.

Verify
npm test
The test script type-checks, builds, and runs the local smoke command.

To run a tool directly without an MCP host:

npm run build
node dist/test-client.js security_check facebook/react
node dist/test-client.js get_license_details expressjs/express
node dist/test-client.js detect_libraries_and_dependencies vercel/next.js
Connect to Claude Desktop
Add this to your Claude Desktop MCP configuration and replace the path with your absolute project path:

{
  "mcpServers": {
    "github-analyzer": {
      "command": "node",
      "args": ["/Users/nitinkumar/GitHUB_Repo_Analyser/dist/index.js"],
      "env": {
        "NODE_ENV": "production",
        "GITHUB_TOKEN": "github_pat_your_token_here"
      }
    }
  }
}
Restart the MCP host after changing the config.

Docker
Build:

docker build -t github-repo-analyzer-mcp .
Run over stdio:

docker run --rm -i \
  -e GITHUB_TOKEN="$GITHUB_TOKEN" \
  github-repo-analyzer-mcp
Environment Variables
Variable	Required	Description
GITHUB_TOKEN	No	GitHub token for higher rate limits or private repository access.
GITHUB_API_TIMEOUT_MS	No	GitHub request timeout, default 15000, max 60000.
GITHUB_USER_AGENT	No	Custom GitHub API user agent.
NODE_ENV	No	Set to production outside development.
Notes
This server is intentionally read-only and local-first. If you convert it to a remote Streamable HTTP MCP server, add HTTPS, authentication, origin validation, rate limiting, OAuth/token audience validation, and deployment monitoring before exposing it publicly.
