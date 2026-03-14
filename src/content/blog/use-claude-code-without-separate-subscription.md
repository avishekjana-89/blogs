---
title: "Use Claude Code Without a Separate Subscription"
description: "Already paying for GitHub Copilot or Google Antigravity? Here's how to route Claude Code through your existing subscription using a local proxy — no extra bill required."
pubDate: "2026-03-08"
tags: ["claude-code", "github-copilot", "antigravity", "proxy", "tutorial"]
---

Claude Code is Anthropic's powerful CLI tool for agentic coding — but it comes with its own subscription. If you're already paying for **GitHub Copilot** or **Google Antigravity**, you can skip the extra cost entirely by running a lightweight local proxy that routes Claude Code's API calls through the subscription you already have.

This guide covers both options step by step.

> [!NOTE]
> **A quick heads-up before you get started.**
> This approach works well in practice, but it's worth knowing that GitHub Copilot and Google Antigravity subscriptions are intended for use within their own ecosystems. Routing a third-party tool like Claude Code through them isn't an officially supported use case, so it technically sits in a policy grey area. In rare cases, accounts have been temporarily suspended — just go in with eyes open and be aware of the risk.

---

## Prerequisites

Before you begin, make sure you have:

- **Node.js** (v18+) installed
- **npm** available globally
- An active **GitHub Copilot** or **Google Antigravity** subscription

Install Claude Code itself if you haven't already:

```bash
npm install -g @anthropic-ai/claude-code
```

---

## Option 1: GitHub Copilot Subscription

GitHub Copilot provides access to Claude models through its API. We'll use the **copilot-api** package to set up a local proxy that translates Claude Code's requests into Copilot-compatible calls.

### Step 1 — Install the Proxy

```bash
npm install -g copilot-api
```

### Step 2 — Start the Proxy

```bash
copilot-api start
```

This runs a local proxy server on `http://localhost:4141`.

### Step 3 — Configure Claude Code

Edit (or create) `~/.claude/settings.json` and add the following environment variables:

```json
{
  "env": {
    "ANTHROPIC_BASE_URL": "http://localhost:4141",
    "ANTHROPIC_AUTH_TOKEN": "sk-dummy",
    "ANTHROPIC_MODEL": "claude-sonnet-4.5",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "gpt-5-mini",
    "DISABLE_NON_ESSENTIAL_MODEL_CALLS": "1",
    "CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC": "1"
  }
}
```

---

## Option 2: Google Antigravity Subscription

If you have a Google Antigravity subscription, you can use the **antigravity-claude-proxy** package to route Claude Code through your existing Google AI quota.

### Step 1 — Install the Proxy

```bash
npm install -g antigravity-claude-proxy@latest
```

### Step 2 — Start the Proxy Server

Start with the default port (`8080`):

```bash
antigravity-claude-proxy start
```

Or specify a custom port:

```bash
PORT=8081 antigravity-claude-proxy start
```

The server runs as a background process by default. To see live logs, add the `--log` flag:

```bash
antigravity-claude-proxy start --log
```

### Step 3 — Link Your Google Account

The proxy needs access to your Google account's AI quota. The easiest way is through the web dashboard:

1. Open **http://localhost:8080** (or your custom port) in your browser
2. Navigate to the **Accounts** tab
3. Click **Add Account**
4. Complete the Google OAuth authorization in the pop-up window

Alternatively, you can add accounts via the CLI:

```bash
antigravity-claude-proxy accounts add
```

### Step 4 — Verify the Proxy

Confirm the proxy is active by testing the health endpoint:

```bash
curl http://localhost:8080/health
```

You should get a healthy response back.

### Step 5 — Configure Claude Code

Edit `~/.claude/settings.json` to point Claude Code at your Antigravity proxy:

```json
{
  "env": {
    "ANTHROPIC_BASE_URL": "http://localhost:8080",
    "ANTHROPIC_AUTH_TOKEN": "sk-placeholder",
    "ANTHROPIC_MODEL": "claude-opus-4-6-thinking",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "claude-opus-4-6-thinking",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "claude-sonnet-4-6-thinking",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "claude-sonnet-4-6",
    "CLAUDE_CODE_SUBAGENT_MODEL": "claude-sonnet-4-6-thinking",
    "DISABLE_NON_ESSENTIAL_MODEL_CALLS": "1",
    "CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC": "1"
  }
}
```

---

## Running Claude Code

With either proxy running and your `settings.json` configured, just launch Claude Code as usual:

```bash
claude
```

Claude Code will route all API calls through your local proxy, which in turn uses your existing Copilot or Antigravity subscription. No separate Anthropic API key or Claude Code subscription needed.

---

## Quick Comparison

| | GitHub Copilot | Antigravity |
|---|---|---|
| **Install** | `npm i -g copilot-api` | `npm i -g antigravity-claude-proxy@latest` |
| **Default port** | 4141 | 8080 |
| **Auth method** | GitHub Copilot token | Google OAuth |
| **Config complexity** | Low | Low |

---

## Tips and Troubleshooting

- **Keep the proxy running.** Claude Code needs the proxy active in the background. If you close the terminal that started the proxy, Claude Code will lose connectivity.
- **Check the port.** Make sure the `ANTHROPIC_BASE_URL` in your settings matches the port your proxy is actually running on.
- **Reduce unnecessary calls.** The `DISABLE_NON_ESSENTIAL_MODEL_CALLS` and `CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC` flags prevent Claude Code from making extra background requests, which keeps your usage within your subscription limits.
- **One proxy at a time.** Only run one proxy at a time, and make sure your `settings.json` points to the correct one.

---

## Wrapping Up

You don't need to pay twice for AI-powered coding. If you already have a GitHub Copilot or Google Antigravity subscription, a simple local proxy lets you use Claude Code at no additional cost. Set it up once, and you're good to go.

Happy coding!
