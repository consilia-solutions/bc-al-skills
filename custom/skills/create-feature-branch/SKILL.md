---
name: create-feature-branch
description: 'Create and validate Git feature branches using the naming convention feature/[Jira Item Key]-the-title-of-the-jira-item. Use when: starting a Jira ticket, creating a branch, checking branch naming compliance, preparing issue work, or fixing invalid branch names.'
argument-hint: 'Jira key, or Jira key plus title (example: BC-123 Add posting diagnostics)'
user-invocable: true
---

# Create Feature Branch

Create a feature branch that follows the required pattern exactly:

feature/[Jira Item Key]-the-title-of-the-jira-item

## Outcome

Produce one valid branch name and create or check out the branch safely.

## Inputs

Required:
- Jira item key (example: BC-123)

Optional but recommended:
- Jira item title (example: Improve posting diagnostics)

## Procedure

1. Gather required values.
- If Jira key is missing, ask for it.
- If title is missing, ask for it.

2. Normalize the Jira key.
- Trim whitespace.
- Validate format as uppercase letters, hyphen, digits (example pattern: ABC-123).
- If key is not uppercase, convert to uppercase and confirm.

3. Slugify the Jira title.
- Convert to lowercase.
- Replace spaces and underscores with hyphens.
- Remove characters other than a-z, 0-9, and hyphen.
- Collapse repeated hyphens.
- Trim leading and trailing hyphens.

4. Assemble the branch name.
- Format: feature/<JIRA-KEY>-<title-slug>
- Example: feature/BC-123-improve-posting-diagnostics

5. Validate against quality checks.
- Starts with feature/
- Contains exactly one Jira key section after feature/
- Contains a non-empty title slug
- Uses only allowed slug characters

6. Handle decision branches.
- If branch already exists locally: checkout instead of create.
- If remote branch exists: do tracking checkout.
- If working tree is dirty: warn user before switching and ask to continue.

7. Execute Git command.
- Create new branch:
  git checkout -b feature/<JIRA-KEY>-<title-slug>
- Or track remote:
  git checkout --track origin/feature/<JIRA-KEY>-<title-slug>

8. Confirm completion.
- Show current branch with: git branch --show-current
- Confirm the final branch equals the generated branch name.

## Decision Logic

- Missing key or title: pause and request the missing input.
- Invalid key format: ask user to confirm the intended Jira key.
- Empty slug after cleanup: request a clearer title.
- Existing branch name conflict: choose checkout or revise title slug.

## Completion Criteria

This skill is complete only when all checks pass:
- A branch name is generated in the exact pattern.
- Branch name passes validation rules.
- Git is on the intended branch.
- The final branch name is shown to the user.

## Examples

Input:
- Key: BC-321
- Title: Add warning for empty filters

Output:
- feature/BC-321-add-warning-for-empty-filters

Input:
- Key: al-42
- Title: Fix: null ref in Post()

Output:
- feature/AL-42-fix-null-ref-in-post
