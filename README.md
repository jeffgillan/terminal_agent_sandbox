## DRAFT – AI Agent Sandboxing 

 

An AI agent is an AI system that can autonomously perform tasks by making decisions, using tools, and taking actions based on its understanding of a goal—rather than simply responding to prompts. Unlike traditional chatbots that only generate text responses, AI agents can interact with software, execute code, modify files, search the web, and coordinate multiple operations to complete complex workflows. Commercial AI agents include Anthropic’s Claude Code, OpenAI’s Codex, and Google’s Gemini CLI. Each of these Agent software programs can be installed locally on a computer and have direct access to the files, tools, and systems on the computer. Agents can mimic nearly anything that a human user can do at a computer terminal.  

This autonomy and tool-use capability is what makes AI agents powerful for productivity. However, an unsupervised agent with unrestricted access could inadvertently modify critical files, expose sensitive data, or execute harmful commands. 

AI sandboxing is a security technique that creates an isolated, controlled environment where AI agents and coding assistants can execute code, access files, and perform operations without risking damage to production systems, sensitive data, or critical infrastructure. Think of it as giving the AI a dedicated "practice room" where it can work freely and make mistakes without consequences, while keeping your actual systems, student data, and research environments completely protected. 

AI Agents that are safe for users starts with the companies that build them. Through application design choices, the companies can give them capabilities or limit capabilities for enhanced security. AI companies have a responsibility to make a tool that is highly useful while limiting the danger it can pose.  For the most part, the companies appear to erring on the side of safety and are releasing the AI Agents with default behaviors that promote safety (e.g., requiring user permission before most actions).  

The following are two general methods that a user can deploy for AI Agent sandboxing: 
 

* Infrastructure Sandboxing - this includes running the AI agents in virtual machines (VMs) or containers (e.g., Docker) to isolate it from the sensitive system files or data on the host.  

* OS level Sandboxing - software (Bubblewrap for Linux, Seatbelt for MacOS) that can restrict access to specified directories and files, networking (i.e., web searches), and tools. These OS level software tools can work directly with commercial AI Agents like Claude Code and Codex by using configuration files (e.g., toml, json).  

 

### Current UA Infrastructure Sandboxing 

#### CyVerse Discovery Environment (DE) 
Offers a secure, remote, platform to work with AI and Agentic AI. Users of the DE can launch containerized Linux terminals and jupyter notebooks with pre-installed AI agents including Claude Code, Codex, and Gemini CLI. Working in containers allows a user to avoid exposing sensitive personal data (on a laptop for example) to an AI that they have given access to their filesystem. 

The DE is: 

* A secure, authenticated environment for isolated workloads 

* An access-controlled container with GPU capabilities 

This system provides: 

* Authentication/Authorization (KeyCloak) - controls who can access 

* Network isolation (port 443 only) - limits exposure 

* Container isolation (K8s pod) - basic process isolation 

* TLS encryption (NGINX on 443) - secure transport 

 

#### UA UITS offers managed cloud services from AWS that can support VMs and development environments which support traditional AI sandbox technology. 

https://it.arizona.edu/managed-cloud-services 

 

#### UA HPC  

 

#### Running AI agents locally in containers – Instead of relying on cloud infrastructure for sandboxing, AI Agents can be run in docker containers on the user’s local machine. Docker is specifically making tools designed for containerizing AI Agents.  

 

 

### OS Level Sandboxing 

After an AI Agent program has been installed on a computer (either locally or on a remote VM), additional restrictions can be put in place to limit its capabilities.  

Key AI sandbox characteristics include: 

* Restricted system calls – limits permission (cannot delete your hard-drive) 

* Limited file system access – can only access a single folder 

* Network isolation controls – can talk to the internet, or to programs on your computer 

* Resource constraints – limit RAM, CPU, disk (cannot crash your computer) 

 

 

Claude Code OS Sandboxing 

Anthropic has developed an open-source OS level sandbox called  Sandbox Runtime (srt).  

The sandbox runtime srt uses native OS sandboxing primitives (sandbox-exec on macOS, bubblewrap on Linux) and proxy-based network filtering. It can be used to sandbox the behavior of agents, local MCP servers, bash commands and arbitrary processes.  

 

Claude Sandboxing 

Claude Sandboxing Settings 

 

Through the use of a simple json file, restrictions on the AI Agent can be set in four locations: 

/<repository>/.claude/settings.local.json. This is sandboxing for a specific project directory only. Managed by the user. Git ignored. 

/<repository>/.claude/settings.json - Project settings (shared with team, checked into git) 

~/.claude/settings.json. Sandboxing for all projects on system 

/etc/claude-code/managed-settings.json. System Administration Sandboxing that rules all other sandboxing. 

 

Here is an example of a json file that will restrict writing and editing access in specific directories. 

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
 

 

Codex OS Sandboxing 

OpenAI’s Codex can do similar sandboxing similar to how Claude does it. Instead of a json config file, it uses a `config.toml` file. Seatbelt policies on macOS and Linux seccomp + landlock enforce local sandboxing. Defaults include no network access and write permissions limited to the active workspace. Users can configure the sandbox, approval, and network security settings based on their risk tolerance. 

https://developers.openai.com/codex/security/ 

 

Gemini CLI OS Sandboxing 

Gemini CLI can use MacOS Seatbelt for Mac users only.  

https://geminicli.com/docs/cli/sandbox/ 

 

Cursor OS Sandboxing (MacOSX, or WSL2.0 on Windows)  

https://cursor.com/docs/agent/terminal#sandbox 

 

 

Layered Security 

For enhanced sandboxing, the two general methods (infrastructure, OS level) can be deployed together.  

 

For example, when you run a commercial AI application (Claude Code, Codex, Gemini) in a Cyverse DE container, it creates a Layered Security Model where: 

Outer layer (K8s pod): Container isolation + KeyCloak auth + network restrictions 

Inner layer (SDK Sandbox): Actual code execution sandbox for AI-generated code 


<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>


## Install Claude Code

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
