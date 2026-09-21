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

  New MCP server found in this project: rhoai-mcp

  MCP servers may execute code or access system resources. All tool calls require approval. Learn more in the MCP documentation.

    Use this MCP server
  ❯ Use this and all future MCP servers in this project
    Continue without using this MCP server

  Enter to confirm · Esc to cancel
```

# 
