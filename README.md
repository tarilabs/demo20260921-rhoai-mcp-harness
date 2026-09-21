This repo contains demo settings for connecting rhoai-mcp inside of different harnesses

# claude code

setup:

```sh
claude mcp add rhoai-mcp \
  --scope project \
  --transport http \
  --url "https://rhoai-mcp-rhoai-mcp.apps.(...redacted...).openshiftapps.com/mcp" \
  --header "Authorization: Bearer \${OCP_TOKEN}"
```

usage:

```
OCP_TOKEN=$(oc whoami -t) claude

❯ using rhoai-mcp which data science projects are available?

  New MCP server found in this project: rhoai-mcp

  MCP servers may execute code or access system resources. All tool calls require approval. Learn more in the MCP documentation.

    Use this MCP server
  ❯ Use this and all future MCP servers in this project
    Continue without using this MCP server

  Enter to confirm · Esc to cancel

Let me check the available data science projects using the RHOAI MCP tools.

There is 1 data science project available on the cluster:

┌───────┬──────────────┬───────────┬────────┐
│ Name  │ Display Name │ Requester │ Status │
├───────┼──────────────┼───────────┼────────┤
│ prova │ prova        │ mmortari  │ Ready  │
└───────┴──────────────┴───────────┴────────┘

It's an empty project (no description) with ModelMesh disabled, in a Ready state.
```

# codex

setup:

.codex/config.toml:

```toml
[mcp_servers.rhoai-mcp]
url = "https://rhoai-mcp-rhoai-mcp.apps.rosa.mmortari-rosa2.54u1.p3.openshiftapps.com/mcp"
http_headers_helper = "/Users/mmortari/git/demo20260921-rhoai-mcp-harness/.codex/rhoai-mcp-headers"
```

rhoai-mcp-headers (script):

```sh
#!/bin/sh
set -eu

token="$(oc whoami -t)"
printf '{"Authorization":"Bearer %s"}\n' "$token"
```

usage:

```
codex

› using the rhoai-mcp, which data science projects are available?


• I’ll query the connected RHOAI MCP service for the available data-science projects.

• Called rhoai-mcp.cluster_summary({})
  └ {"projects": 1, "project_names": ["prova"], "workbenches": "0/0 running", "models": "0/0 ready", "pipelines": 0, "storage": 0, "data_connections": 0}

• The available data science project is prova.

  It currently has no workbenches, deployed models, pipelines, storage volumes, or data connections.
```

Notes:
Codex caches helper headers, then re-runs the helper after a same-origin 401 or 403; that is the documented token-refresh behavior for http_headers_helper. [Official OpenAI documentation](https://learn.chatgpt.com/docs/extend/mcp?surface=cli#cli-streamable-http-servers).
Alternatively, do similar setup of exporting OCP_TOKEN, and setup config.toml to use the Env variable.

# opencode

setup:

opencode.json:

```json
{
  "mcp": {
    "servers": {
      "rhoai-mcp": {
        "type": "remote",
        "url": "https://rhoai-mcp-rhoai-mcp.apps.rosa.mmortari-rosa2.54u1.p3.openshiftapps.com/mcp",
        "oauth": false,
        "headers": {
          "Authorization": "Bearer {env:OCP_TOKEN}"
        }
      }
    }
  }
}
```

```
export OCP_TOKEN="$(oc whoami -t)"
opencode service restart
opencode reload
opencode mcp list
opencode
```

usage:

```
using the rhoai-mcp, list available data science projects
✓execute
›search [query=data science projects, namespace=rhoai-mcp, limit=10]
✓execute
›rhoai-mcp.list_data_science_projects [verbosity=standard]
Available Data Science Projects:
- prova — Ready, ModelMesh disabled, requester: mmortari
Build · GPT-5.6 Terra · 5.9s · 25.3 tok/s
```

Note: seems like opencode sources the token from the service setup invocations

