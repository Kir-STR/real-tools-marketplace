# Real Supervisor (RS) Plugin for Claude Code

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Version](https://img.shields.io/badge/version-1.0.0-blue.svg)](https://github.com/Kir-STR/claude-real-tools/releases)
[![GitHub issues](https://img.shields.io/github/issues/Kir-STR/claude-real-tools)](https://github.com/Kir-STR/claude-real-tools/issues)
[![GitHub stars](https://img.shields.io/github/stars/Kir-STR/claude-real-tools)](https://github.com/Kir-STR/claude-real-tools/stargazers)

A powerful Claude Code plugin that orchestrates complex, multi-step projects through a structured, resumable workflow with specialized worker agents.

## Overview

The **Real Supervisor Plugin** implements the **Supervisor Pattern** - it acts as an intelligent orchestrator that decomposes complex tasks, delegates to specialized worker agents, and manages the entire lifecycle from requirements analysis to final delivery.

### Key Features

- **File-Based Operations**: All inputs and outputs are file-based for transparency and version control
- **Resumable Workflows**: Robust state persistence enables seamless resumption after interruptions
- **Spec-Driven Communication**: Structured, contract-based handoffs between agents ensure reliability
- **Human-in-the-Loop**: Built-in approval gates at critical decision points (plan approval, draft review)
- **Checkpointing**: Integration with Claude Code's `/rewind` for artifact versioning
- **Specialized Agents**: Pre-defined agents (Analyst, Designer, Implementer, Writer, Tester, Critique)
- **Command Alias**: Use the short `rs` command instead of typing `supervisor`

## Plugin Components

- **1 skill**: `real-supervisor` - Orchestrates the 14-step workflow
- **7 agents**: Explore, Analyst, Designer, Implementer, Writer, Critique, Tester
- **2 commands**: `/rs` (quick alias) and `/supervisor` (full name)
- **3 hooks**:
  - `SubagentStop` - Logs agent outputs for audit trail
  - `SessionStart` - Checks for interrupted sessions on startup
  - `PostToolUse` - Validates `.supervisor/` file integrity

## Installation

### Prerequisites

- Claude Code CLI installed and configured
- Basic familiarity with command line operations

### Recommended: Install via Marketplace

The easiest way to install the plugin is through the Claude Code marketplace:

```bash
# Add the marketplace
/plugin marketplace add Kir-STR/claude-real-tools

# Install the plugin
/plugin install real-supervisor@real-tools

# Verify installation
/plugins
```

This method automatically handles updates and version management.

### Alternative Installation Methods

#### Option 1: Clone and Symlink (Development)

For local development or testing:

```bash
# Clone the repository
git clone https://github.com/Kir-STR/claude-real-tools.git

# Create symlink to plugins directory
# Linux/Mac
ln -s $(pwd)/claude-real-tools ~/.config/claude-code/plugins/real-supervisor

# Windows (PowerShell, as Administrator)
New-Item -ItemType SymbolicLink -Path "$env:APPDATA\claude-code\plugins\real-supervisor" -Target "$(Get-Location)\claude-real-tools"
```

#### Option 2: Manual Copy

```bash
# Clone the repository
git clone https://github.com/Kir-STR/claude-real-tools.git

# Copy to plugins directory
# Linux/Mac
cp -r claude-real-tools ~/.config/claude-code/plugins/real-supervisor

# Windows (PowerShell)
Copy-Item -Recurse claude-real-tools "$env:APPDATA\claude-code\plugins\real-supervisor"
```

### Post-Installation

1. **Reload plugins** in Claude Code (if not using marketplace method):
   ```
   /reload-plugins
   ```

2. **Verify installation**:
   ```
   /plugins
   ```
   You should see `real-supervisor` listed among available plugins.

3. **Test the installation**:
   ```bash
   # Using the full command
   supervisor

   # Or using the short alias
   rs
   ```

## Quick Start

### 1. Create a Product Requirements Document (PRD)

Create a markdown file describing what you want to build. Here's a minimal structure:

```markdown
# Project Title

## Overview
Brief description of what you're building

## Objectives
What you want to achieve

## Functional Requirements
- Requirement 1
- Requirement 2
- ...

## Deliverable
What the final output should be
```

### 2. Invoke the Supervisor

```bash
# Using the full command
supervisor path/to/your_prd.md

# Or using the short alias
rs path/to/your_prd.md
```

### 3. Follow the Workflow

The supervisor will guide you through:

1. **Exploration**: Analyzes your PRD and requirements
2. **Planning**: Creates an execution plan and presents it for approval
3. **Specification**: Produces a detailed technical specification
4. **Draft Creation**: Creates a first version of your deliverable
5. **Review**: Presents the draft for your feedback
6. **Final Delivery**: Incorporates feedback and delivers the final result

## Workflow Phases

```
┌─────────────────────────────────────────────────────────────────┐
│                    USER INVOKES SUPERVISOR                       │
│                         rs path/to/prd.md                        │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
                   PHASE 0: Initialization
                             │
                             ▼
                   PHASE 1: Explore & Analyze
                             │
                             ▼
                   PHASE 2: Plan & Define Schema
                             │
                             ▼
                   PHASE 3: Critique & Approval (HIL)
                             │
                             ▼
                   PHASE 4: Generate Specification
                             │
                             ▼
                   PHASE 5: Checkpoint Initial
                             │
                             ▼
                   PHASE 6: Create Draft
                             │
                             ▼
                   PHASE 7: Review Draft (HIL)
                             │
                             ▼
                   PHASE 8: Checkpoint Draft
                             │
                             ▼
                   PHASE 9: Deliver Final Version
                             │
                             ▼
                   PHASE 10: Completion
```

### Phase 0: Initialization
- Checks for existing session state
- Offers to resume interrupted sessions
- Initializes new session if starting fresh

### Phase 1-3: Explore & Plan
- Analyzes PRD using Explore agent
- Creates detailed execution plan
- Defines specification schema for the task
- Critiques the plan
- **[HIL Gate]** Presents plan for your approval

### Phase 4-6: Specification & Draft
- Produces structured specification conforming to schema
- **[Checkpoint]** Creates initial checkpoint
- Generates draft deliverable (code, design, documentation)

### Phase 7-9: Review & Final
- **[HIL Gate]** Presents draft for your review and feedback
- **[Checkpoint]** Creates draft checkpoint
- Incorporates feedback into final version
- Delivers polished final result

### Phase 10: Completion
- Archives session log
- Presents all deliverables
- Marks session as complete

## Session Resumption

If your session is interrupted, simply re-invoke the supervisor:

```bash
rs path/to/your_prd.md
```

The supervisor will:
1. Detect the interrupted session
2. Show you which step was last completed
3. Ask if you want to resume from that point
4. Continue seamlessly from where you left off

All session state is stored in `.supervisor/state.json` and `.supervisor/session_log.json`.

## Using Checkpoints

The supervisor creates two checkpoints:

### Initial Checkpoint (Before Draft Execution)
- Created before the draft phase begins
- Use `/rewind` to return if you want to change the specification

### Draft Checkpoint (Before Final Execution)
- Created after you review the draft
- Use `/rewind` to return if the final version has issues

To rewind to a checkpoint:
```
/rewind
```
Follow the prompts to select which checkpoint to restore.

## Worker Agents

The Real Supervisor uses specialized worker agents that are automatically discovered from the `agents/` directory:

- **Explore** - PRD analysis and requirements extraction
- **Analyst** - Requirements analysis and specification generation
- **Designer** - UI/UX designs, architecture diagrams, system designs
- **Implementer** - Code implementation
- **Writer** - Documentation and technical writing
- **Critique** - Critical review and feedback
- **Tester** - Test plan and test case generation

Claude Code automatically selects the appropriate agent based on task characteristics. All agents are available for use throughout the workflow as needed.

You can also modify the agent definitions in the `agents/` directory or add your own custom agents.

## Output Files

All outputs are organized in `.supervisor/`:

```
.supervisor/
├── state.json                    # Current workflow state
├── session_log.json              # Agent execution history
└── output/
    ├── exploration_report.md     # PRD analysis
    ├── plan.md                   # Execution plan
    ├── spec_schema.md            # Specification schema
    ├── plan_critique.md          # Plan review
    ├── task_spec.md              # Detailed specification
    ├── draft_*                   # Draft deliverable
    ├── feedback.md               # Your review feedback
    └── final_*                   # Final deliverable
```

## Examples

### Example 1: Building a REST API

```bash
# Create your PRD
cat > api_prd.md << 'EOF'
# Task Management REST API

## Overview
Build a RESTful API for task management with CRUD operations.

## Requirements
- User authentication with JWT
- Task CRUD endpoints
- Task filtering and search
- PostgreSQL database

## Deliverable
Complete Node.js/Express application with API endpoints
EOF

# Run the supervisor
rs api_prd.md
```

The supervisor will:
1. Analyze requirements
2. Create implementation plan
3. Present plan for approval
4. Generate detailed specification
5. Create draft implementation
6. Ask for your review
7. Deliver final code

### Example 2: Creating a System Design

```bash
# Create design PRD
cat > design_prd.md << 'EOF'
# E-Commerce Platform Architecture

## Overview
Design the system architecture for a scalable e-commerce platform.

## Requirements
- Microservices architecture
- Handle 10,000 concurrent users
- Support multiple payment gateways
- Real-time inventory management

## Deliverable
Comprehensive architecture document with diagrams and component descriptions
EOF

# Run the supervisor
rs design_prd.md
```

The supervisor will use the Designer agent to create architectural designs.

### Example 3: Resuming After Interruption

```bash
# Original invocation (interrupted during draft creation)
rs project_prd.md
# ... session interrupted ...

# Later, resume the session
rs project_prd.md
# Supervisor detects previous session and asks to resume
# Select "Yes" to continue from where you left off
```

### Example 4: Sample PRD Template

```markdown
# Product Requirements Document: Task Management API

## Overview
We need to build a RESTful API for a task management system that allows
users to create, read, update, and delete tasks. The API should support
task categorization, priority levels, and due dates.

## Objectives
1. Provide a simple, intuitive API for task management
2. Support multiple users with authentication
3. Enable task organization through categories and priorities
4. Allow filtering and searching of tasks

## Functional Requirements

### FR-1: Task CRUD Operations
- Users must be able to create new tasks with title, description,
  category, priority, and due date
- Users must be able to read their own tasks (individual and list views)
- Users must be able to update task properties
- Users must be able to delete tasks they own

### FR-2: User Authentication
- Users must authenticate using JWT tokens
- API must support user registration and login
- Each user can only access their own tasks

## Non-Functional Requirements

### NFR-1: Performance
- API response time: < 200ms for single task operations
- API response time: < 500ms for list operations
- Support at least 100 concurrent users

### NFR-2: Security
- All endpoints except registration/login require authentication
- Passwords must be hashed using bcrypt
- JWT tokens expire after 24 hours

## Deliverable
A complete Node.js/Express application with:
- Source code organized in MVC structure
- Database schema and models
- API endpoints implementation
- Basic documentation of endpoints
- Instructions for local setup and running
```

## Advanced Usage

### Modifying the Specification Schema

If the default schema doesn't fit your needs, you can provide a custom schema:

1. Create your own `custom_schema.md`
2. Reference it in your PRD under a "Specification Schema" section
3. The supervisor will use your custom schema instead

### Providing Early Feedback

At the plan approval stage (Step 7), you can:
- Approve the plan as-is
- Request specific changes to the plan
- Cancel if the approach isn't suitable

This ensures you're aligned with the approach before implementation begins.

### Iterating on Drafts

At the draft review stage (Step 11), you can:
- Approve the draft and proceed to final version
- Provide detailed feedback for improvements
- Reject the draft and request a complete redo

Your feedback is saved to `feedback.md` and incorporated into the final version.

## Troubleshooting

### Issue: "No PRD file found at path"
**Solution**: Ensure the PRD file path is correct and the file exists.

### Issue: Agent fails during execution
**Solution**: The supervisor will log the error and ask if you want to retry, modify inputs, or cancel. Check the session log at `.supervisor/session_log.json` for details.

### Issue: Can't resume previous session
**Solution**: Ensure `.supervisor/state.json` exists. If corrupted, delete it and start a new session.

### Issue: Final deliverable doesn't match expectations
**Solution**: Use `/rewind` to return to the draft checkpoint, provide more detailed feedback, and regenerate the final version.

### Issue: Plugin not appearing after installation
**Solution**:
1. Verify the plugin is in the correct plugins directory
2. Try `/reload-plugins` or restart Claude Code
3. Check Claude Code logs for errors

## Configuration

The plugin includes several configuration options:

### Command Alias

The plugin provides a short `rs` alias for convenience. You can use either:
- `supervisor path/to/prd.md` (full command)
- `rs path/to/prd.md` (short alias)

### Customizing Agents

Edit `.claude/skills/real-supervisor/agents.md` to:
- Add new specialized agents
- Modify existing agent instructions
- Change agent selection criteria

## Best Practices

1. **Write detailed PRDs**: More detail in requirements = better results
2. **Review plans carefully**: The plan approval gate is your chance to shape the approach
3. **Provide specific feedback**: At draft review, be specific about what needs to change
4. **Use checkpoints**: Don't hesitate to `/rewind` if something goes wrong
5. **Check session logs**: `.supervisor/session_log.json` tracks all agent activity
6. **Organize PRDs**: Keep your PRD files in a dedicated directory for easy management

## Limitations

The supervisor explicitly avoids work not required by the PRD:
- Won't create extensive documentation beyond specifications
- Won't perform complex analytics unless requested
- Won't create deployment pipelines or CI/CD unless specified in PRD
- Won't create automated test suites unless explicitly required

Focus is on core task execution from PRD to deliverable.

## Architecture

The supervisor implements a state machine with:
- 14 distinct steps across 10 phases
- State persistence after every step
- Session logging for all agent invocations
- Two mandatory HIL approval gates
- Two checkpoints for rollback capability
- Spec-driven agent communication

## Directory Structure

After installation, the plugin structure is:

```
real-supervisor/
├── .claude/
│   ├── settings.json                            # Project settings
│   └── skills/
│       └── real-supervisor/                     # The supervisor skill
│           ├── SKILL.md                         # Core supervisor instructions
│           ├── agents.md                        # Custom agent definitions
│           └── examples/                        # Example files
│               ├── example_prd.md
│               ├── example_state.json
│               ├── example_session_log.json
│               ├── example_spec_schema.md
│               └── workflow_diagram.md
├── .claude-plugin/
│   ├── plugin.json                              # Plugin manifest
│   └── marketplace.json                         # Marketplace metadata
├── commands/
│   ├── supervisor.md                            # /supervisor command
│   └── rs.md                                    # /rs command (alias)
└── hooks/
    ├── hooks.json                               # Plugin hooks configuration
    └── README.md                                # Hooks documentation
```

## Contributing

Contributions are welcome! To extend the plugin:

1. **Add custom agents**: Edit `.claude/skills/real-supervisor/agents.md`
2. **Modify workflow**: Update `.claude/skills/real-supervisor/SKILL.md`
3. **Enhance state tracking**: Add fields to the state schema
4. **Create new schemas**: Add task-specific spec schemas

Please submit pull requests with:
- Clear description of changes
- Test results showing workflow still functions
- Updated documentation if needed

## License

MIT License - see LICENSE file for details.

## Support

For issues, questions, or feature requests:
- Open an issue on GitHub
- Check existing issues for solutions
- Review the documentation in `.claude/skills/real-supervisor/examples/`

## Version

Current version: 1.0.0

## Acknowledgments

Built on Claude Code's powerful agent orchestration capabilities. Inspired by the supervisor pattern in software architecture and the need for robust, resumable workflows in complex project execution.

---

**Quick Reference**:
- Install: `/plugin marketplace add Kir-STR/claude-real-tools` → `/plugin install real-supervisor@real-tools`
- Usage: `/rs path/to/prd.md` or `/supervisor path/to/prd.md`
- Resume: Re-run same command after interruption
- Checkpoints: Use `/rewind` to return to saved states
- Output: Check `.supervisor/output/` for all generated files
- Marketplace: See [MARKETPLACE.md](MARKETPLACE.md) for distribution details
