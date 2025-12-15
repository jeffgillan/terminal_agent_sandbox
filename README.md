# Install Claude Code

Install claude code:

`curl -fsSL https://claude.ai/install.sh | bash`

Install socat and bubblewrap

bubblewrap - Creates isolated namespaces for filesystem and network restrictions

socat - Handles socket communications between the sandbox and Claude Code

`sudo apt update`

`sudo apt install socat bubblewrap`

launch claude in a repo `claude'


## Sandboxing 

Preventing Claude from accessing specific directories, files, tools, or networks. 

[Claude Sandboxing](https://code.claude.com/docs/en/sandboxing)

[Claude Sandboxing Settings](https://code.claude.com/docs/en/settings#sandbox-settings)



Typing `/sandbox` while in a Claude Code session will generate the file `settings.local.json` located at `/<repository>/.claude/settings.local.json`. This is sandboxing for specific repo. 


### 4 Locations for Sandboxing

1. `/<repository>/.claude/settings.local.json`. This is sandboxing for a specific project directory only. Managed by the user. Git ignored.
2. `/<repository>/.claude/settings.json` - Project settings (shared with team, checked into git)
3. `~/.claude/settings.json`. Sandboxing for all projects on system
4. `/etc/claude-code/managed-settings.json`. System Admininstration Sandboxing that rules all other sandboxing.  


For example this `managed-settings.json` will prevent all write and edit access to any Community folders in the Cyverse DE. 

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
      "Write(~/data-store/home/shared/**)",
      "Edit(~/data-store/home/shared/**)",
      "Write(~/data-store/data/iplant/home/shared/**)",
      "Edit(~/data-store/data/iplant/home/shared/**)"
    ]
  }
}
```
