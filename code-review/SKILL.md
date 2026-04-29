# Code Review Skill

This skill enables AI agents to perform structured, actionable code reviews across pull requests and code snippets. It covers style, logic, security, and performance concerns.

## Overview

| Property       | Value                          |
|----------------|--------------------------------|
| Skill ID       | `code-review`                  |
| Category       | Engineering                    |
| Complexity     | Intermediate                   |
| Auth Required  | Depends on provider            |
| Supported LLMs | GPT-4o, Claude 3.5, Gemini 1.5 |

---

## Description

The **Code Review** skill instructs an agent to analyze source code and produce structured feedback. The agent checks for:

- **Correctness** – logic errors, off-by-one bugs, unhandled edge cases
- **Security** – injection risks, hardcoded secrets, insecure dependencies
- **Performance** – unnecessary loops, blocking calls, memory leaks
- **Readability** – naming conventions, dead code, missing docstrings
- **Style** – adherence to language-specific best practices (PEP 8, ESLint, etc.)

---

## System Prompt

```
You are an expert code reviewer. When given a code snippet or diff, produce a structured review using the following format:

## Summary
A 2–3 sentence overview of the code and its purpose.

## Issues
For each issue found, provide:
- **Severity**: Critical | High | Medium | Low | Info
- **Location**: file name and line number (if available)
- **Description**: what the problem is and why it matters
- **Suggestion**: concrete fix or improvement

## Positives
Highlight 1–3 things the author did well.

## Overall Score
Rate the code on a scale of 1–10 with a one-line justification.

Be concise, constructive, and specific. Avoid vague feedback like "improve naming".
```

---

## Usage

### Input Schema

```json
{
  "code": "string (required) — raw source code or unified diff",
  "language": "string (optional) — e.g. python, typescript, go",
  "context": "string (optional) — brief description of what the code does",
  "focus": ["security", "performance", "style"] 
}
```

### Output Schema

```json
{
  "summary": "string",
  "issues": [
    {
      "severity": "Critical | High | Medium | Low | Info",
      "location": "string",
      "description": "string",
      "suggestion": "string"
    }
  ],
  "positives": ["string"],
  "score": {
    "value": 7,
    "justification": "string"
  }
}
```

---

## Example

**Input:**
```python
def get_user(id):
    query = "SELECT * FROM users WHERE id = " + id
    return db.execute(query)
```

**Output (abbreviated):**

> **Summary**: A function that fetches a user record from the database by ID.
>
> **Issues**:
> - **Critical** | Line 2 | SQL injection vulnerability — user input is concatenated directly into the query. Use parameterised queries: `db.execute("SELECT * FROM users WHERE id = ?", (id,))`
>
> **Score**: 3/10 — Critical security flaw makes this unsafe for production.

---

## References

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [Google Engineering Practices – Code Review](https://google.github.io/eng-practices/review/)
- [PEP 8 – Python Style Guide](https://peps.python.org/pep-0008/)
