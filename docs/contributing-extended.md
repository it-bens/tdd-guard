# Extended Contributing Guidelines

This file contains detailed guidelines that were removed from the main CONTRIBUTING.md to keep it minimal. These can be added back in future PRs as needed.

## Detailed Commit Message Guidelines

### Structure

```
Title: Brief description in imperative mood (under 70 characters)

Body:
Explain WHY the changes are needed (4-5 sentences max).
Reference relevant issues, meetings, or discussions.
Keep lines under 70 characters for readability.
You are writing this text for a reviewer. Don't make their life hard.

For implementation details see [ISSUE-NUMBER].
```

### Requirements

**Title:**

- Use English and imperative language ("Add feature" not "Added feature")
- Answer "WHAT?" - describe what the commit does
- Keep under 70 characters
- No issue numbers in the title
- Use appropriate scopes for monorepo structure:
  - Single package: `feat(go): add error handling`
  - Multiple packages: `feat: add error handling across reporters`
  - Breaking changes: `feat!: change shared contract interface`
  - Reporter-specific: `pytest: Add support for...` (legacy format accepted)

**Body:**

- Explain "WHY?" - provide context for the changes
- Reference issues, emails, or meetings with specific identifiers
- Use professional, neutral language
- Break lines at ~70 characters
- Provide appropriate context based on change complexity (see Context Guidelines below)

**Example Good Commits:**

```
feat(go): implement pass-through output for real-time test visibility

Users can now see test progress in real-time while the reporter processes
results. The reporter acts as a transparent filter, immediately passing
all output to stdout while simultaneously capturing it for processing.

This ensures users maintain visibility of test execution without delays,
addressing a key usability requirement for the Go reporter.
```

```
fix(docs): correct stderr redirection syntax in Go examples

The 2>&1 redirection must come after the test target (./...) for
proper shell syntax. Incorrect placement would cause the command
to fail.
```

```
chore(cli): bump version to 0.9.1

Release includes Go reporter improvements and build failure handling.
See CHANGELOG.md for detailed changes.
```

### Context Guidelines

Provide appropriate context based on your change:

- **Simple fixes/typos**: Brief explanation acceptable
- **Feature additions**: Explain user benefit and approach
- **Architecture changes**: Detailed reasoning required
- **Cross-language/reporter changes**: Impact on all affected components
- **Documentation updates**:
  - User-facing docs (README, setup): Explain user impact
  - API/code docs: Technical context required
  - Typo fixes/formatting: Minimal context acceptable

Focus on WHY over WHAT. Write for reviewers and future maintainers.

### What to Avoid

- Vague titles like "fixed stuff" or "updates"
- Multiple unrelated changes in one commit
- Missing context about why changes were made
- Unprofessional language or jokes
- Lines exceeding 70 characters
- Mixing different types of changes (bug fixes + new features + refactoring)
- Adding fixes for previous commits. Just amend them yourself. Please.
