# ai-tools

A collection of Claude Code plugins distributed via a marketplace hosted in this repository.

---

## Install the marketplace

In the Claude Code console, run:

```
/plugin marketplace add armandoayala/ai-tools
```

This registers the `armlabs-ai-tools` marketplace from GitHub. Verify it was added:

```
/plugin marketplace list
```

---

## Install a plugin

### quality-review-plugin

Provides a `solid-review` skill that analyzes source code for compliance with SOLID principles and generates a scored Markdown report.

Install it from the marketplace:

```
/plugin install quality-review-plugin@armlabs-ai-tools
```

Then reload plugins in the current session:

```
/reload-plugins
```

The skill is now available. Trigger it by asking Claude to review code, e.g.:

> "Review my code for SOLID principles" or "Check this file: src/services/UserService.java"
