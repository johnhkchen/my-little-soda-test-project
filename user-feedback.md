# User Test Report: my-little-soda init command

## Test Environment
- Platform: Linux x86-64
- Date: 2025-08-26
- Command tested: `./my-little-soda init`

## Key Issues Found

### 1. Legacy Naming Inconsistency
The tool still references the old project name "clambake" in multiple places:
- Error message: "Run setup: clambake init"
- Config path: ".clambake/credentials/github_owner"
- Log messages: "Clambake telemetry initialized" and "Clambake telemetry shutdown complete"

### 2. Configuration Requirements
- Missing GITHUB_OWNER and GITHUB_REPO environment variables
- Suggests creating `.clambake/credentials/github_owner` (should probably be `.my-little-soda/`)
- Configuration error: "missing field `owner`"

## Positive Observations

### 3. Good Error Handling
- Binary executable runs successfully
- Provides clear error messages with helpful quick fixes
- Suggests multiple solutions: environment variables, GitHub CLI, or setup command

### 4. Validation Flow
- GitHub CLI authentication check works: "✅ Verifying GitHub CLI authentication... ✅"
- Repository permissions validation attempted: "✅ Checking repository write permissions..."
- Clean structured logging output with timestamps

### 5. User Experience
- Clear phase-based output: "Phase 1: Validation"
- Helpful emoji indicators and formatting
- Comprehensive setup information displayed

## Recommendations

1. **URGENT: Complete rebrand from "clambake" to "my-little-soda"**
   - Config directory: `.clambake/` → `.my-little-soda/`
   - All log messages referencing "Clambake"
   - Configuration field names and error messages
   - Internal telemetry and database references

2. **Improve init command user experience**
   - Provide clear prerequisite checklist before running init
   - Consider auto-initializing git repository if none exists
   - Offer to create GitHub repository during init process
   - Better error messages explaining what setup steps are missing

3. **Fix GitHub integration issues**
   - **Label creation error handling**: Tool fails when trying to create labels that already exist
   - **Configuration persistence**: Creates `.clambake/credentials/` directory but doesn't properly save config
   - **Missing field `owner` error**: Configuration file format appears broken or incomplete
   - **Poor error recovery**: Tool exits completely on label creation failure instead of continuing
   - Improve repository permission validation
   - Handle existing vs new repository scenarios better

## Critical Issue: Git Repository Assumptions

### 6. Fresh Project vs Existing Repository Conflict
- The tool assumes it's running in an existing git repository
- Error shows "✅ Checking repository write permissions..." but we're not in a git repo
- This creates a chicken-and-egg problem: 
  - Fresh projects (like this "obsidian" directory) have no git repo
  - Tool expects to validate repository permissions before proceeding
  - Unclear if init should initialize a git repo or requires one to exist

### 7. Repository Existence Assumptions
- Tool tries to check permissions on "johnhkchen/obsidian" repository
- This repository doesn't exist on GitHub yet
- Error: "Failed to access repository: GitHub. Check your GitHub token permissions."
- The tool should either:
  - Create the GitHub repository as part of init
  - Clearly indicate it requires an existing GitHub repository
  - Work locally first before requiring remote repository

### 8. **CRITICAL: Widespread Legacy "Clambake" References**
After proper GitHub repository setup, the tool reveals extensive old project name usage:
- **Config directory**: Creates `.clambake/` instead of `.my-little-soda/`
- **Error messages**: "Failed to initialize configuration: Failed to load configuration: missing field `owner`"
- **Log output**: Multiple "Clambake telemetry" references throughout execution
- **Help text inconsistency**: Tool says "My Little Soda" in description but creates clambake directories
- This is confusing for users who expect consistency with the tool name

### 9. Git Repository Setup Requirements
Tool requires specific git setup sequence:
1. `git init` (local repository)
2. Clean git state (committed files)
3. `git remote add origin` (remote repository link)
4. GitHub repository must exist before init
Tool provides minimal guidance on these prerequisites

### 10. GitHub API Integration Issues
- **Labels created successfully** on first run (confirmed 21 labels created)
- **Subsequent runs fail** trying to recreate existing labels with generic "GitHub" error
- **Dry-run shows good planning** but doesn't detect existing labels
- **Configuration corruption**: "missing field `owner`" error persists despite environment variables
- Tool should detect and skip existing labels rather than failing completely
- **Incomplete setup**: Tool creates directory structure but doesn't complete configuration

## Overall Assessment

The tool shows good error handling and helpful guidance for users, but has several critical issues:
1. Legacy naming creates confusion about what tool the user is actually running
2. **Major design flaw**: Assumes existing git repository when "init" suggests it should work in fresh projects
3. The init command provides clear feedback and next steps, but may not work in the intended use case of initializing a new project