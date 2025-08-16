# Debugging TDD Guard

This guide helps you troubleshoot common issues with TDD Guard.

## Quick Checks

1. Verify Claude Code is running
2. Check hook installation
3. Confirm test files exist

## Common Issues

### Hook Not Running

If TDD Guard isn't intercepting operations:

- Check `.claude/hooks/` directory exists
- Verify hook file permissions
- Restart Claude Code

## Troubleshooting Steps

### 1. Verify Installation

```bash
npm list tdd-guard
```

### 2. Check Configuration

Ensure your project has the required test framework setup:

- Jest: `package.json` includes jest dependency
- Vitest: `vitest.config.ts` exists
- Go: `*_test.go` files present
- Python: `test_*.py` files present

### 3. Debug Mode

Enable debug logging:

```bash
export TDD_GUARD_DEBUG=true
```
