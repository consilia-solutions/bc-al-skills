# Copilot Instructions for BCQuality Repository

## Core Rule: Custom Folder Only

**All AI-generated changes to this repository must be made exclusively in the `/custom/` folder. Do not modify any other directories.**

## What This Means

### ✓ Allowed
- Create, modify, or delete files in `/custom/knowledge/` and `/custom/skills/`
- Add new knowledge files to `/custom/knowledge/`
- Add new action skills to `/custom/skills/`
- Update the `/custom/README.md` to document your changes

### ✗ Forbidden
- **Never modify** `/microsoft/` — this is official Microsoft guidance
- **Never modify** `/community/` — this is community-contributed content
- **Never modify** `/skills/` (global skills)
- **Never modify** `/evaluation/`, `/tools/`, or any root-level files
- **Never modify** `.github/` workflows, actions, or configurations
- **Never modify** `README.md`, `LICENSE`, `CODEOWNERS`, or other repository metadata

## File Organization in `/custom/`

```
custom/
├── README.md                    # Document your customizations here
├── knowledge/                   # Your knowledge files
│   └── [domain]/               # Organize by domain (e.g., performance, security)
│       ├── [topic].md          # Follow the same markdown + YAML structure as microsoft/
│       ├── [topic].good.al     # Good example code
│       └── [topic].bad.al      # Anti-pattern example code
└── skills/                      # Your action skills
    └── [skill-name]/
        └── SKILL.md            # Custom action skill
```

## Guidelines

1. **Follow conventions**: Use the same frontmatter, markdown structure, and YAML format as `/microsoft/knowledge/` and `/microsoft/skills/`
2. **Layer hierarchy**: If a `/custom/` file has the same name and domain as one in `/microsoft/`, it will take precedence (custom overrides microsoft)
3. **Reference original**: When extending or specializing existing guidance, note in your file that it customizes or extends the official guidance
4. **Keep it isolated**: All your work stays in `/custom/` so it doesn't interfere with official content or other contributors

## When to Refuse

If asked to modify anything outside `/custom/`, politely decline and explain:
- "I can only make changes in the `/custom/` folder to keep this repo clean. Let me create that in `/custom/` instead."
- Suggest creating a custom variant in `/custom/knowledge/` or `/custom/skills/` as an alternative

## Example Scenario

**Request**: "Add a check for performance optimization"

**Response**: 
- ✓ Create `/custom/knowledge/performance/my-custom-optimization-rule.md`
- ✓ Create or update `/custom/skills/my-custom-performance-skill.md`
- ✗ Do NOT modify `/microsoft/knowledge/performance/`
- ✗ Do NOT modify `/microsoft/skills/`
