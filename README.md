# Real Tools Marketplace

A Claude Code plugin marketplace providing tools for orchestrating complex projects through structured, repeatable workflows.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Version](https://img.shields.io/badge/version-1.0.0-blue.svg)](https://github.com/Kir-STR/real-tools-marketplace/releases)

## Overview

**Real Tools** is a marketplace that hosts Claude Code plugins designed to enhance project orchestration capabilities. Each plugin provides a collection of components (skills, agents, commands, hooks) that work together to solve specific workflow challenges.

## Available Plugins

### Real Tools

A powerful orchestration plugin that manages complex, multi-step projects through a structured, resumable workflow with specialized worker agents.

**Components:**
- 1 skill: `supervisor` (14-step workflow orchestration)
- 7 agents: Explore, Analyst, Designer, Implementer, Writer, Critique, Tester
- 2 commands: `/supervisor`
- 3 hooks: SubagentStop, SessionStart, PostToolUse

**Documentation:** [plugins/real-tools/README.md](./plugins/real-tools/README.md)

## Repository Structure

```
real-tools-marketplace/               # Marketplace root
├── .claude-plugin/
│   └── marketplace.json              # Marketplace configuration
├── plugins/
├── CLAUDE.md                         # Instructions for Claude Code
├── LICENSE                           # MIT License
└── README.md                         # This file
```

## Version Information

- **Marketplace Version**: 1.0.0
- **Repository**: https://github.com/Kir-STR/real-tools-marketplace

## License

MIT License - see [LICENSE](./LICENSE) file for details.

## Support

For issues, questions, or feature requests:
- Open an issue on [GitHub](https://github.com/Kir-STR/real-tools-marketplace/issues)
- Check existing issues for solutions
- Review plugin-specific documentation in `plugins/*/README.md`

## Author

**Kirill Bogdanov**

Marketplace: `real-tools-marketplace`

---

**Quick Reference:**
- Add marketplace: `/plugin marketplace add Kir-STR/real-tools-marketplace`
- Install plugin: `/plugin install real-tools@real-tools-marketplace`
- Plugin docs: [plugins/real-tools/README.md](./plugins/real-tools/README.md)
