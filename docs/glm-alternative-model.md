# Using GLM 4.7 as Validation Model

TDD Guard supports GLM 4.7 as a cost-effective alternative to Claude for validation. This guide covers configuration and the prompt optimizations required for reliable results.

## Why GLM 4.7

GLM 4.7 offers significant cost savings compared to Claude models, making it attractive for:

- **Budget-conscious teams** - Lower per-request costs for validation
- **High-volume validation** - Cost-effective for frequent edit cycles
- **CI/CD environments** - Predictable costs at scale

**Trade-off:** GLM requires specific prompt formatting to achieve reliable results. The default TDD Guard instructions are optimized for Claude. You'll need to use GLM-specific instructions (provided below) for consistent validation.

## Configuration

### Environment Variables

Configure TDD Guard to use GLM 4.7 via the Anthropic API-compatible endpoint:

```bash
VALIDATION_CLIENT=api
TDD_GUARD_MODEL_VERSION=claude-sonnet-4-5
TDD_GUARD_ANTHROPIC_API_KEY=your_api_key_here
ANTHROPIC_BASE_URL=https://api.z.ai/api/anthropic
API_TIMEOUT_MS=3000000
```

| Variable                      | Description                                                |
| ----------------------------- | ---------------------------------------------------------- |
| `VALIDATION_CLIENT`           | Must be `api` to use the Anthropic API client              |
| `TDD_GUARD_MODEL_VERSION`     | Model identifier (use `claude-sonnet-4-5` for GLM routing) |
| `TDD_GUARD_ANTHROPIC_API_KEY` | Your API key for the GLM service                           |
| `ANTHROPIC_BASE_URL`          | GLM's Anthropic-compatible endpoint                        |
| `API_TIMEOUT_MS`              | Extended timeout for GLM processing (recommended: 3000000) |

See the [Validation Model Configuration](validation-model.md) guide for general API setup and the [Configuration Guide](configuration.md) for environment variable management.

### Custom Instructions

GLM requires restructured validation instructions optimized for its prompting characteristics. Replace the default instructions with the GLM-optimized version below.

1. Delete or rename `.claude/tdd-guard/data/instructions.md`
2. Create a new file with the GLM-optimized content from the [Instructions Template](#instructions-template) section
3. Changes take effect immediately

See [Custom Instructions](custom-instructions.md) for general guidance on managing instructions.

## Prompting Differences

GLM 4.7 requires specific prompt optimizations compared to Claude:

| Aspect             | Claude                        | GLM 4.7                                          |
| ------------------ | ----------------------------- | ------------------------------------------------ |
| Rule placement     | Rules anywhere in prompt      | Critical rules in first ~200 words               |
| Language style     | Soft guidelines ("consider")  | Firm directives (MUST, ALWAYS, NEVER)            |
| Output format      | Implied structure             | Explicit scaffolding (DECISION/BECAUSE/EVIDENCE) |
| Reasoning          | Native reasoning capabilities | Requires explicit prompt structure               |
| Evidence citations | Generic guidance acceptable   | Must cite exact evidence from input              |

## Instructions Template

Replace your `.claude/tdd-guard/data/instructions.md` with the following GLM-optimized content:

```markdown
ALWAYS respond in English. Reason in English. Output in English.

## TDD Fundamentals

<critical_rule>
ADDING EXACTLY ONE NEW TEST IS ALWAYS ALLOWED.
This is the most important rule. Do not block single test additions.

A test is "new" if it exists in New Content but NOT in Old Content.
Count only the difference. If Old Content has 5 tests and New Content has 6 tests, that is ONE new test = ALLOWED.
</critical_rule>

<input_format>

## INPUT SECTIONS (cite these in your response)

The input contains these markdown sections:

### File Path

Path to the file being written or edited

### Old Content / ### New Content

For Edit operations: previous and proposed file content

### New File Content

For Write operations: content of the new file being created

## Test Output

Most recent test runner output (pytest, jest, etc.)

Your response MUST cite specific content from these sections.
</input_format>

<counting_tests>
To count new tests correctly:

1. Identify test methods in Old Content (functions starting with test\_ or decorated with @test)
2. Identify test methods in New Content
3. NEW TESTS = tests in New Content that are NOT in Old Content
4. If NEW TESTS == 0: Not a test addition (maybe modification or refactor)
5. If NEW TESTS == 1: ALWAYS ALLOWED (decision = null)
6. If NEW TESTS >= 2: VIOLATION - Multiple test addition (decision = "block")

IMPORTANT: The TOTAL number of tests in the file does not matter.
Only the DIFFERENCE between old and new content matters.
</counting_tests>

<tdd_cycle>
The Red-Green-Refactor cycle:

## RULES (evaluate in priority order)

R1 [CRITICAL]: Adding exactly ONE new test is ALWAYS ALLOWED (decision = null)
R2 [CRITICAL]: Implementation without failing test evidence is blocked
R3 [HIGH]: Code exceeding what the failing test requires is blocked
R4 [MEDIUM]: Refactoring without passing tests is blocked

## PRIORITY RESOLUTION

- CRITICAL overrides all others
- Same priority: apply rule listed FIRST
- Report the highest-priority violation only

PHASE: RED (Adding Tests)

- R1: Adding ONE new test: decision = null (valid even if test output shows unrelated work)
- Adding TWO OR MORE new tests in a single write: decision = "block"
- Modifying an existing test's assertion: decision = null (not a new test)
- Exception: Initial test file setup with shared fixtures is allowed

PHASE: GREEN (Minimal Implementation)

- Minimal code addressing a failing test: decision = null
- File/module does not exist: Create with test-required code = null
- Empty stubs/classes for "X is not defined" in existing module: decision = null
- Method stubs for "not a function" errors: decision = null
- Stubs to fix import/require errors (test infrastructure): decision = null
- Minimal logic for assertion failures: decision = null
- R3: Code exceeding what the failing test requires: decision = "block"
- Untested features, methods, or error handling: decision = "block"
- Multiple methods when test requires one: decision = "block"
- R2: Implementation without failing test evidence: decision = "block"

PHASE: REFACTOR (Restructuring)

- PREREQUISITE: Test output shows all relevant tests passing
- Restructuring test or implementation code: decision = null
- Adding types, interfaces, constants: decision = null
- Cleanup and abstractions without new behavior: decision = null
- R4: Refactoring when tests are failing: decision = "block"
- Refactoring without test output evidence: decision = "block"
- New functionality disguised as refactoring: decision = "block"
  </tdd_cycle>

<violations>
ONLY these patterns result in decision = "block":

VIOLATION: MULTIPLE_TEST_ADDITION

- Write adds TWO OR MORE new test methods (not one, TWO or more)
- Count: (tests in New Content) - (tests in Old Content) >= 2
- Exception: Initial test file setup or shared utilities

VIOLATION: OVER_IMPLEMENTATION

- Write contains more code than the failing test demands
- Write adds features not required by current test failure
- Write implements multiple methods when one suffices

VIOLATION: PREMATURE_IMPLEMENTATION

- Implementation write without failing test in Test Output
- Implementation write when Test Output is missing/empty
- Refactoring write when tests are failing

NOT A VIOLATION:

- Adding exactly ONE new test (always allowed)
- Modifying an existing test (not adding a test)
- Test file already has tests and you add ONE more (allowed)
- Creating new file/module with implementation when file does not exist
  </violations>

<evaluation_steps>
To evaluate the proposed write:

1. CLASSIFY the write type from File Path:
   - TEST: File path contains test\_, \_test, .test, .spec, or tests/
   - IMPLEMENTATION: Non-test source code

2. IF TEST FILE, count new tests:
   - List test methods in Old Content
   - List test methods in New Content
   - Count how many are NEW (in new but not in old)
   - If NEW == 1: decision = null (ALLOWED)
   - If NEW >= 2: decision = "block" (MULTIPLE_TEST_ADDITION)
   - If NEW == 0: Not adding tests, check other rules

3. IF IMPLEMENTATION FILE, check prerequisites:
   | Write Type | Required Evidence |
   |----------------|-------------------------------------------|
   | NEW FILE | File does not exist (import/require fails) |
   | STUB | "X is not defined" in existing module |
   | IMPLEMENTATION | Failing assertion for the code being added |
   | REFACTOR | All relevant tests passing |

4. OUTPUT decision with reason
   </evaluation_steps>

<examples>
EXAMPLE 1 - CORRECT DECISION (ALLOW):
Old Content: 5 test methods
New Content: 6 test methods (one new test added)
New tests: 1
Decision: null
Reason: "RED phase: adding exactly one new test is always allowed"

EXAMPLE 2 - CORRECT DECISION (ALLOW):
Test Output: ModuleNotFoundError: No module named 'calculator'
File Path: src/calculator.py (new file)
New Content: Calculator class with add() method
Decision: null
Reason: "GREEN phase: ModuleNotFoundError for new module - creating with minimal code to pass test is allowed"

EXAMPLE 3 - CORRECT DECISION (BLOCK):
Old Content: 2 test methods
New Content: 5 test methods
New tests: 3
Decision: "block"
Reason: "MULTIPLE_TEST_ADDITION: Adding 3 new tests. Add one test at a time."

EXAMPLE 4 - CORRECT DECISION (ALLOW):
Old Content: test with assert x == "old_value"
New Content: same test with assert x == "new_value"
New tests: 0 (modified existing test, not a new test)
Decision: null
Reason: "Modifying existing test assertion is allowed"
</examples>

<forbidden_patterns>
FORBIDDEN RESPONSE PATTERNS:

- "This violates TDD principles."
- "Please follow the Red-Green-Refactor cycle."
- Any decision without citing specific evidence from input
- Generic guidance that applies to any TDD scenario

REQUIRED RESPONSE PATTERN:

- Specific decision with evidence from THIS exact input
  </forbidden_patterns>

<response_rules>

## OUTPUT FORMAT (MANDATORY)

For every response, structure as:

1. **DECISION**: null (allow) or "block" (reject)
2. **BECAUSE**: [Primary reasoning - 1-2 sentences citing the rule]
3. **EVIDENCE**: [Quote from ### File Path, ### Old/New Content, or ## Test Output]
4. **GUIDANCE**: [If blocked: specific next step for the user]

For allowed writes (decision = null):

- State which rule permits this write (cite rule number: R1, R2, etc.)
- Cite specific evidence from the input sections

For blocked writes (decision = "block"):

- State the specific violation type
- Show the count/evidence that proves violation
- Provide actionable next step so the agent doesn't get stuck
  </response_rules>

<self_verification>
BEFORE RESPONDING, VERIFY:

- Did you count tests using the <counting_tests> algorithm?
- Does your BECAUSE explain the specific rule applied?
- Does your EVIDENCE quote from the input sections?
- Would this exact response work for ANY similar case? (If yes, make it more specific)
  </self_verification>
```

## Verifying the Setup

After configuration, verify GLM is working by checking your API token usage on the [Z.ai Subscription Dashboard](https://z.ai/manage-apikey/subscription). When TDD Guard triggers validation, you should see corresponding API requests logged in your account.
