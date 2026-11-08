# Task: Build and Run a Reusable Review Prompt

`/root/bugcalc/calc.py` is a small calculator module with at least one real bug in it. Build a review prompt you could reuse on any file, then run it against this one.

## Part 1: The prompt file

Create `/root/prompts/code-review.md`. It must instruct the reviewer to:

* begin the output with the exact line `REVIEW NOTES`
* name each file reviewed
* list each issue with a severity (high, medium, or low)

Write it once, properly: this is the file you would keep for every future review.

## Part 2: Run it

Use the prompt file in a headless run against `/root/bugcalc/calc.py`, telling the agent the file's name, and save the reply to `/root/bugcalc/review.txt`. A good review should catch what is wrong with the arithmetic in `calc.py`; read the output and see.

### Given file: `/root/bugcalc/calc.py`

```python
"""Tiny calculator used by the billing scripts."""

def add(a, b):
    return a + b

def subtract(a, b):
    return a + b

def divide(a, b):
    return a / b

if __name__ == "__main__":
    print(add(2, 3))
    print(subtract(10, 4))
    print(divide(9, 3))
```

---

# Solution

## Part 1: Create `/root/prompts/code-review.md`

```bash
mkdir -p /root/prompts
cat > /root/prompts/code-review.md << 'EOF'
# Code Review Prompt

You are a careful code reviewer. Review the file(s) the user names, and
read each file in full before commenting.

## Output format (follow exactly)

1. The very first line of your reply must be exactly:

   REVIEW NOTES

   Nothing may come before it: no greeting, preamble, or markdown heading.

2. Next, add a line `Files reviewed:` followed by the name of each file
   you reviewed, one per line.

3. Then list every issue you find. For each issue give:
   - **Severity**: high, medium, or low
   - **Location**: function name or line number
   - **Problem**: what is wrong and why it matters
   - **Fix**: a concrete suggested change

## Severity guide

- high: wrong results, crashes, data loss, or security problems
- medium: unhandled edge cases or likely failures (e.g. bad input, division by zero)
- low: style, naming, missing docs or tests, minor cleanup

## What to check

- Correctness: does each function do what its name and docs say?
  Check the arithmetic and logic of every function.
- Error handling and edge cases
- Readability and maintainability

## Rules

- Only report real issues; do not invent problems.
- If a file has no issues, say so explicitly under its name.
- Do not modify any files. Report only.
EOF
```

## Part 2: Headless run

```bash
cd /root/bugcalc

claude -p "$(cat /root/prompts/code-review.md)

Review this file: calc.py" > /root/bugcalc/review.txt
```

If your setup prefers loading the prompt as a system prompt, an equivalent form is:

```bash
claude -p --append-system-prompt "$(cat /root/prompts/code-review.md)" \
  "Review this file: calc.py" > /root/bugcalc/review.txt
```

## Verify

```bash
head -1 /root/bugcalc/review.txt   # must print exactly: REVIEW NOTES
cat /root/bugcalc/review.txt
```

## Expected review content

```text
REVIEW NOTES
Files reviewed:
calc.py

1. Severity: high
   Location: subtract(a, b)
   Problem: Returns a + b instead of a - b, so subtract(10, 4) gives 14
            instead of 6. Billing results would be wrong.
   Fix: return a - b

2. Severity: medium
   Location: divide(a, b)
   Problem: No handling of b == 0; raises ZeroDivisionError.
   Fix: Check for zero and raise a clear ValueError, or document the behavior.

3. Severity: low
   Location: module level
   Problem: Functions lack docstrings and there are no tests; the __main__
            demo would not have caught the subtract bug.
   Fix: Add docstrings and unit tests (e.g. assert subtract(10, 4) == 6).
```

## The bug

`subtract` uses `+` instead of `-`. It is the arithmetic error the task hints at, and the main finding a good review must report as **high** severity.