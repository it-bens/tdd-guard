# Monorepo Contributing Guidelines

This file contains monorepo-specific requirements that were removed from the main CONTRIBUTING.md to keep it minimal. These can be added back in future PRs as needed.

## General Requirements

- **Contribution Process**:
  - **Internal contributors**: Create feature branches and submit pull requests directly
  - **External contributors**: Fork the repository first, then create feature branches and submit pull requests

## Cross-Reporter Compatibility

- Changes to shared contracts must maintain compatibility with all reporters
- Don't break existing functionality

## Pull Request Requirements

Before submitting your PR:

1. All commits follow the message guidelines
2. `npm run checks` passes without errors (typecheck, lint, format, test)
3. `npm run build` completes successfully
4. All reporter tests pass with `npm run test:reporters`
5. Branch created from `main`
6. Package-specific tests pass for affected reporters
7. Node.js version compatibility verified (18+)
8. Claude Code hook integration tested manually
9. If changing shared contracts, all reporters tested
10. Package versions updated appropriately (if releasing)

## Monorepo Requirements

### Package Structure

- **JavaScript/TypeScript reporters**: Must import shared functionality from 'tdd-guard' package only
- **Other language reporters**: Must be self-contained with no JavaScript dependencies
- **Shared contracts**: Test result JSON format must remain consistent across all reporters
- **Package independence**: Each reporter must maintain its own version, changelog, and documentation

### Cross-Package Changes

- **Breaking changes to shared contracts**: Use `feat!:` prefix and test all reporters
- **Multi-language compatibility**: Test affected reporters before committing
- **Version coordination**: Update package versions consistently when releasing
- **Documentation updates**: Update both main README and affected reporter READMEs

## Security Requirements

- Never execute untrusted code or commands
- Validate all file paths before operations
- Ensure reporters only write to designated output paths (`.claude/tdd-guard/data/test.json`)
- Document any new permissions or access requirements in commit message
