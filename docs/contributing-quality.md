# Quality Contributing Guidelines

This file contains comprehensive quality standards that were removed from the main CONTRIBUTING.md to keep it minimal. These can be added back in future PRs as needed.

## Code Quality Standards

Every commit must meet these requirements:

1. **Stable State**: Each commit should leave the codebase in a working, stable state
2. **Quality Assurance**: Run `npm run checks` - it must pass without errors
3. **Build Tests**: The package must build successfully with `npm run build`
4. **Hook Compatibility**: The Claude Code hook must function correctly with every commit
5. **Cross-Reporter Compatibility**: Changes to shared contracts must maintain compatibility with all reporters
6. **Don't break**: The existing functionality should not break

## Quality Checklist

Use this checklist for each commit:

- [ ] Commit has clear, imperative title under 70 characters
- [ ] Body provides appropriate context for change complexity
- [ ] Professional language used throughout
- [ ] Lines broken at ~70 characters for readability
- [ ] References to relevant issues/meetings included (where applicable)
- [ ] Appropriate scope used for monorepo structure
- [ ] Code passes `npm run checks`
- [ ] Build completes with `npm run build`
- [ ] Changes are logically grouped (not mixing unrelated modifications)
- [ ] There are no fixes for previous commits in new commits
- [ ] Cross-reporter compatibility considered and tested
- [ ] TypeScript types are properly defined (no `any` without justification)
- [ ] Tests added/updated for new functionality
- [ ] Package boundaries respected (no cross-language dependencies)

## Development Guidelines

### Testing

#### Guidelines

- **Use test helpers**: Extract setup logic into helper functions placed at the bottom of test files
- **Use test factories**: Always use factories from `test/utils/` instead of creating data inline
- **Group tests effectively**: Use `describe` blocks and `beforeEach` for common setup
- **Keep tests concise**: Keep as little logic in the tests themselves as possible

#### Commands

```bash
npm run build             # Build main package and workspace reporters (jest, vitest)
npm run test              # All unit tests and base integration tests
npm run test:unit         # Fast unit tests only
npm run test:integration  # Slow integration tests (run after major prompt changes)
npm run test:reporters    # Test all reporter implementations
npm run lint              # Check code style and quality
npm run format            # Auto-format code with Prettier
npm run checks            # Run all checks: typecheck, lint, format, and test
```

### Key Design Principles

- **Interface-driven**: Core functionality defined by interfaces (`Storage`, `ModelClient`)
- **Dependency injection**: Components receive dependencies as parameters
- **Single responsibility**: Each module has one clear purpose
- **Type safety**: Comprehensive TypeScript types with runtime validation
