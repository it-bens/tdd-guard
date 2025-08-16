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
