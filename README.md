<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>GitHub Repo Analyzer MCP Server</title>
    <style>
      :root {
        color-scheme: light;
        --bg: #f7f8fa;
        --panel: #ffffff;
        --text: #18202a;
        --muted: #5d6875;
        --border: #d9dee7;
        --code-bg: #111827;
        --code-text: #e5edf6;
        --accent: #0f766e;
      }

      body {
        margin: 0;
        background: var(--bg);
        color: var(--text);
        font-family: Arial, Helvetica, sans-serif;
        line-height: 1.6;
      }

      main {
        max-width: 960px;
        margin: 0 auto;
        padding: 48px 24px 72px;
      }

      article {
        background: var(--panel);
        border: 1px solid var(--border);
        border-radius: 8px;
        padding: 36px;
      }

      h1,
      h2 {
        line-height: 1.2;
      }

      h1 {
        margin: 0 0 16px;
        font-size: 2rem;
      }

      h2 {
        margin-top: 36px;
        border-top: 1px solid var(--border);
        padding-top: 28px;
        font-size: 1.35rem;
      }

      p {
        color: var(--muted);
      }

      code {
        border-radius: 4px;
        background: #edf2f7;
        color: #1f2937;
        padding: 2px 5px;
        font-family: Menlo, Consolas, Monaco, monospace;
        font-size: 0.92em;
      }

      pre {
        overflow-x: auto;
        border-radius: 8px;
        background: var(--code-bg);
        color: var(--code-text);
        padding: 18px;
      }

      pre code {
        background: transparent;
        color: inherit;
        padding: 0;
      }

      table {
        width: 100%;
        border-collapse: collapse;
        margin: 18px 0;
      }

      th,
      td {
        border: 1px solid var(--border);
        padding: 10px 12px;
        text-align: left;
        vertical-align: top;
      }

      th {
        background: #eef6f5;
        color: #123b37;
      }

      ul {
        padding-left: 22px;
      }

      li {
        margin: 6px 0;
      }

      .lead {
        font-size: 1.05rem;
      }

      .note {
        border-left: 4px solid var(--accent);
        background: #eef9f7;
        padding: 12px 16px;
        color: #214541;
      }
    </style>
  </head>
  <body>
    <main>
      <article>
        <h1>GitHub Repo Analyzer MCP Server</h1>
        <p class="lead">
          A local Model Context Protocol (MCP) server that exposes read-only GitHub repository analysis tools over
          <code>stdio</code>. It can be connected to Claude Desktop, Cursor, VS Code, or any MCP-compatible host that
          supports local servers.
        </p>

        <h2>Tools</h2>
        <table>
          <thead>
            <tr>
              <th>Tool</th>
              <th>Purpose</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td><code>analyze_repo_structure</code></td>
              <td>File counts, directory overview, file-type distribution, and largest files.</td>
            </tr>
            <tr>
              <td><code>get_repo_insights</code></td>
              <td>Language mix, complexity, tests, docs, CI, and Docker signals.</td>
            </tr>
            <tr>
              <td><code>get_license_details</code></td>
              <td>License detection, SPDX ID, and practical usage implications.</td>
            </tr>
            <tr>
              <td><code>detect_libraries_and_dependencies</code></td>
              <td>Dependency and major framework detection across common ecosystems.</td>
            </tr>
            <tr>
              <td><code>security_check</code></td>
              <td>Basic repository safety heuristics and risk rating.</td>
            </tr>
            <tr>
              <td><code>explain_neural_network_if_present</code></td>
              <td>ML/NN framework and architecture pattern detection.</td>
            </tr>
          </tbody>
        </table>

        <p>All tools accept one argument:</p>
        <pre><code>{
  "repo": "owner/repo"
}</code></pre>

        <h2>Production Hardening Included</h2>
        <ul>
          <li>Strict <code>owner/repo</code> input validation.</li>
          <li>GitHub API timeout configuration.</li>
          <li>GitHub <code>User-Agent</code> header.</li>
          <li>Safe MCP error responses that avoid leaking stack traces, tokens, or internal details.</li>
          <li><code>stderr</code>-only server logging so <code>stdio</code> JSON-RPC is not corrupted.</li>
          <li>Malformed remote <code>package.json</code> handling.</li>
          <li>Truncated GitHub tree detection to avoid misleading partial analysis.</li>
          <li>Strict TypeScript build.</li>
          <li><code>.gitignore</code>, <code>.dockerignore</code>, <code>.env.example</code>, and Docker packaging.</li>
        </ul>

        <h2>Requirements</h2>
        <ul>
          <li>Node.js <code>&gt;=18.14.1</code></li>
          <li>npm</li>
          <li>Optional GitHub token for higher rate limits or private repositories.</li>
        </ul>

        <h2>Setup</h2>
        <pre><code>npm ci
cp .env.example .env
npm run build</code></pre>

        <p>
          For public repositories, <code>GITHUB_TOKEN</code> is optional. For production use or private repositories,
          set a fine-grained token:
        </p>

        <pre><code>export GITHUB_TOKEN=github_pat_your_token_here</code></pre>

        <p>For private repositories, grant read-only repository Contents access.</p>

        <h2>Run Locally</h2>
        <pre><code>npm start</code></pre>
        <p>
          The server runs on <code>stdio</code>. It will not print normal logs to <code>stdout</code> because that
          channel is reserved for MCP protocol messages.
        </p>

        <h2>Verify</h2>
        <pre><code>npm test</code></pre>
        <p>The test script type-checks, builds, and runs the local smoke command.</p>

        <p>To run a tool directly without an MCP host:</p>
        <pre><code>npm run build
node dist/test-client.js security_check facebook/react
node dist/test-client.js get_license_details expressjs/express
node dist/test-client.js detect_libraries_and_dependencies vercel/next.js</code></pre>

        <h2>Connect to Claude Desktop</h2>
        <p>Add this to your Claude Desktop MCP configuration and replace the path with your absolute project path:</p>

        <pre><code>{
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
}</code></pre>

        <p>Restart the MCP host after changing the config.</p>

        <h2>Docker</h2>
        <p>Build:</p>
        <pre><code>docker build -t github-repo-analyzer-mcp .</code></pre>

        <p>Run over <code>stdio</code>:</p>
        <pre><code>docker run --rm -i \
  -e GITHUB_TOKEN="$GITHUB_TOKEN" \
  github-repo-analyzer-mcp</code></pre>

        <h2>Environment Variables</h2>
        <table>
          <thead>
            <tr>
              <th>Variable</th>
              <th>Required</th>
              <th>Description</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td><code>GITHUB_TOKEN</code></td>
              <td>No</td>
              <td>GitHub token for higher rate limits or private repository access.</td>
            </tr>
            <tr>
              <td><code>GITHUB_API_TIMEOUT_MS</code></td>
              <td>No</td>
              <td>GitHub request timeout, default <code>15000</code>, max <code>60000</code>.</td>
            </tr>
            <tr>
              <td><code>GITHUB_USER_AGENT</code></td>
              <td>No</td>
              <td>Custom GitHub API user agent.</td>
            </tr>
            <tr>
              <td><code>NODE_ENV</code></td>
              <td>No</td>
              <td>Set to <code>production</code> outside development.</td>
            </tr>
          </tbody>
        </table>

        <h2>Notes</h2>
        <p class="note">
          This server is intentionally read-only and local-first. If you convert it to a remote Streamable HTTP MCP
          server, add HTTPS, authentication, origin validation, rate limiting, OAuth/token audience validation, and
          deployment monitoring before exposing it publicly.
        </p>
      </article>
    </main>
  </body>
</html>
