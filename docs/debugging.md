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

## Example Scenarios

### Scenario 1: Tests Not Detected

**Problem**: TDD Guard says "No tests found"

**Solution**:

1. Check test file naming conventions
2. Verify test directory structure
3. Ensure test files contain actual test functions

Example correct structure:

```
src/
  utils.ts
test/
  utils.test.ts  # or utils.spec.ts
```

### Scenario 2: Hook Permissions Error

**Problem**: Permission denied when accessing hooks

**Solution**:

```bash
chmod +x .claude/hooks/*
```

### Scenario 3: False Positives

**Problem**: TDD Guard blocks valid operations

**Solution**:

1. Check if tests truly cover the functionality
2. Verify test assertions are meaningful
3. Consider test quality, not just quantity
