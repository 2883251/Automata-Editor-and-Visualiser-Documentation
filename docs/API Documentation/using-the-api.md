# Using the API

The Automata Editor has one endpoint made for use from outside the app: **marking**. You send it a Turing machine and a list of test cases, and it tells you which cases pass. A script, a marking tool or a CI job can call it with an **API key**, without anyone signing in.

This page takes you from nothing to a working request. The full reference is in [REST Endpoints](rest-endpoints.md#marking-apimark).

---

## What You Need

- An account on the [Automata Editor](https://automata-ev-gghea7fxbsh9b5hx.southafricanorth-01.azurewebsites.net). You sign in once to create a key.
- Something that can send an HTTP request. The examples use `curl`.

The API is at:

```text
https://automata-ev-server-dwengvgxevg3hpbz.southafricanorth-01.azurewebsites.net
```

To check it is up, open `/health` on that address. It answers `{"status":"ok"}`.

---

## Step 1: Create a Key

1. Open the Automata Editor and **sign in**.
2. Press the **key icon** in the navigation bar. On a phone it is at the bottom of the menu. It opens the **API Keys** dialog.
3. Type a label that says what the key is for, such as `Assignment 2 marking`, and press **Create**.
4. Press **Copy** and keep the key somewhere safe.

The key is shown **once**. The server keeps only a scrambled copy of it, so nobody can show it to you again. If you lose it, revoke it and create another.

A key is 64 letters and digits. Anyone who has it can use the marking endpoint as you, so do not commit it to a repository or paste it into a chat.

---

## Step 2: Write the Request

Save this as `request.json`. It holds the machine's instructions as text, and two test cases:

```json
{
  "machine": {
    "kind": "source",
    "text": "states:\n  q0\n  yes\n  no\n\nstart: q0\naccept: yes\nreject: no\n\ntape_alphabet: '_', '0', '1'\ninput_alphabet: '0', '1'\n\ntransitions:\n  (q0, '0') -> (q0, '0', R)\n  (q0, '1') -> (no, '1', R)\n  (q0, '_') -> (yes, '_', R)\n"
  },
  "testCases": [
    { "input": "000", "expectation": { "kind": "accepts" } },
    { "input": "010", "expectation": { "kind": "rejects" } }
  ]
}
```

- `text` is exactly what the **Instruction Editor** shows for the machine, with each line break written as `\n`. This one is the **Only zeros** example from the home page. The app's **Help** dialog, under **API keys**, shows the same request ready to copy.
- Each test case has an `input` and what you expect the machine to do with it.

| `kind` | The case passes when |
|---|---|
| `accepts` | The machine halts in an accepting state |
| `rejects` | The machine halts in a rejecting state, or stops because no transition applies |
| `final-tape` | The tape the machine leaves matches `tape`. Blanks at either end are ignored, and so is where on the tape it sits |

A `final-tape` case looks like this:

```json
{ "input": "01", "expectation": { "kind": "final-tape", "tape": "11" } }
```

---

## Step 3: Send It

Put your key in the `X-API-Key` header:

```bash
curl -X POST https://automata-ev-server-dwengvgxevg3hpbz.southafricanorth-01.azurewebsites.net/api/mark \
  -H "Content-Type: application/json" \
  -H "X-API-Key: YOUR_KEY" \
  -d @request.json
```

---

## Step 4: Read the Reply

```json
{
  "results": [
    {
      "testCase": { "input": "000", "expectation": { "kind": "accepts" } },
      "outcome": "accepted",
      "passed": true,
      "measurement": { "kind": "measured", "inputLength": 3, "steps": 4, "cells": 5 }
    },
    {
      "testCase": { "input": "010", "expectation": { "kind": "rejects" } },
      "outcome": "rejected",
      "passed": true,
      "measurement": { "kind": "measured", "inputLength": 3, "steps": 2, "cells": 3 }
    }
  ],
  "summary": { "total": 2, "passed": 2, "failed": 0, "excluded": 0 }
}
```

- `results` has one entry per test case, in the order you sent them.
- `passed` is the verdict for that case.
- `outcome` is what the machine did: `accepted`, `rejected`, `stuck` (no transition applied) or `exceeded-limit` (it was still running at the step limit).
- `measurement` is the cost of the run: `steps` taken and tape `cells` the head visited.
- `summary` counts the cases. `excluded` counts the ones that hit the step limit. They are neither passed nor failed.

A `final-tape` case that fails also says where the tape first differs:

```json
"comparison": {
  "expected": "11",
  "actual": "01",
  "origin": 0,
  "firstDifference": 0,
  "firstDifferencePosition": 0
}
```

---

## The Step Limit

A case that has not halted after **10,000 steps** is stopped and reported as `exceeded-limit`. To change the limit for a request, add `maxSteps` beside `testCases`:

```json
{ "machine": { "...": "..." }, "testCases": [ "..." ], "maxSteps": 100000 }
```

The most it can be is 10,000,000.

---

## When Something Goes Wrong

Every error is JSON with an `error` code and a `detail` that explains it.

| Status | `error` | What happened | What to do |
|---|---|---|---|
| `401` | `UNAUTHENTICATED` | No `X-API-Key` header was sent | Add the header |
| `403` | `FORBIDDEN` | The key is wrong or has been revoked | Check the key, or create a new one |
| `400` | `VALIDATION_ERROR` | The request is not shaped as above, for example no test cases. `issues` lists each problem | Fix the request |
| `400` | `PARSE_ERROR` | The instructions have a syntax error. `diagnostics` lists each one with its line and column | Fix the instructions |
| `400` | `INVALID_MACHINE` | The instructions parse but do not describe a valid machine, for example an accepting state that is never declared | Fix the machine |

A syntax error comes back like this:

```json
{
  "error": "PARSE_ERROR",
  "detail": "Source text contains syntax errors",
  "diagnostics": [
    {
      "severity": "error",
      "message": "Expected direction 'L', 'R' or 'S', got 'identifier' at 'X'",
      "range": { "start": 180, "end": 181, "line": 15, "column": 26 }
    }
  ]
}
```

---

## Revoking a Key

Open the **API Keys** dialog and press **Revoke** next to the key. It stops working straight away, and anything still using it gets `403`.

---

## Good to Know

- Marking saves nothing. The machine and the test cases are used for the reply and then forgotten.
- Marking runs a machine as a deterministic one. For a [nondeterministic machine](../Features/nondeterminism.md) it follows the first transition written for each choice, so its verdicts are not meaningful there.
- A `final-tape` expectation checks the first tape only.
- The rest of the API (saving, sharing and opening machines) is used by the app itself and needs a sign-in token, not an API key. It is described in [REST Endpoints](rest-endpoints.md).

---

**Related**: [REST Endpoints](rest-endpoints.md) | [Authentication & Security](authentication.md) | [Test Cases](../Features/test-cases.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Claude Code [Claude Opus 5.5].
