# terminal_agent_sandbox


Install claude code:

`curl -fsSL https://claude.ai/install.sh | bash`


Install socat and bubblewrap

bubblewrap - Creates isolated namespaces for filesystem and network restrictions

socat - Handles socket communications between the sandbox and Claude Code

`sudo apt update`

`sudo apt install socat bubblewrap`

launch claude in a repo 'claude'


## Un-sandboxed

[https://code.claude.com/docs/en/sandboxin](Claude Sandboxing)

[https://code.claude.com/docs/en/settings#sandbox-settings]{Claude Sandboxing Settings}



`/sandbox` 

When you first launch Claude in a repo, it will generate the file `settings.local.json` located at `/<repository>/.claude/settings.local.json`. This appears to be sandboxing for specific repo. 

But we need global sandboxing. You can set enterprise sandbox settings by creating this file `/etc/claude-code/managed-settings.json`. Once set, individual repos have to follow this sandboxing rules. 

For example this `managed-settings.json` will prevent all write access to the system

```
{
  "sandbox": {
    "enabled": true,
    "autoAllowBashIfSandboxed": false,
    "excludedCommands": [],
    "network": {
      "allowUnixSockets": [],
      "allowLocalBinding": false
    }
  },
  "permissions": {
    "deny": [
      "Write(**)",
      "Edit(**)"
    ]
  }
}
```
