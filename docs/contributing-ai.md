# AI-Assisted Contributing Guidelines

This file contains AI collaboration guidelines that were removed from the main CONTRIBUTING.md to keep it minimal. These can be added back in future PRs as needed.

## AI-Assisted Development

AI collaboration is welcomed and encouraged. Contributors may include AI assistance acknowledgments in commit messages to maintain transparency about development methods.

**Acceptable AI signatures:**

- "Generated with [Claude Code](https://claude.ai/code)"
- "Co-Authored-By: Claude <noreply@anthropic.com>"
- "Implemented with AI assistance"

**Guidelines:**

- Focus commit message body on business reasoning (human decision-making)
- AI signatures acknowledge tool usage, not decision ownership
- Technical implementation details can reference AI assistance

**Example AI-assisted commit:**

```
feat(go): implement pass-through output for real-time test visibility

Users can now see test progress in real-time while the reporter processes
results. The reporter acts as a transparent filter, immediately passing
all output to stdout while simultaneously capturing it for processing.

This ensures users maintain visibility of test execution without delays,
addressing a key usability requirement for the Go reporter.

Generated with [Claude Code](https://claude.ai/code)
Co-Authored-By: Claude <noreply@anthropic.com>
```

Note: These guidelines balance code quality with development efficiency, supporting both AI-assisted and traditional development workflows. They enable clear communication while avoiding unnecessary friction, helping maintain TDD Guard's high velocity and multi-language architecture.
