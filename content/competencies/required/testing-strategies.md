+++
draft = false
title = 'Testing Strategies'
status = 'pending'
+++

> Identify a component of your project that you were responsible for testing.
>
> - According to code coverage tools, what aspects of this component are covered with tests?
> - What kinds of tests are they?
> - For code that is not covered by tests, why did you not cover them?

## Testing Strategies Sample

### Location

`backend/tests/test_app.py` (Flask backend, `backend/src/`) and `backend/.coveragerc`

### My contributions

Claude Code and I wrote the backend test suite and set up the coverage.

### Generative AI use

Claude Code and Cowork worked with me extensively to develop a comprehensive test suite.

### Competency Demonstration

Claude Code and I wrote the tests for a newly secured Flask backend with 107 tests spanning 27 classes. The coverage of these is 445 of 445 statements and 92 of 92 branches (so 100%), and `branch = True` means that it checks every if/else path and not just every line. Most are API tests that send requests through Flask’s test client against a temporary SQLite copy, so the real database is never actually touched. Others are config tests and time tests (a mocked clock), and many are threat-based, with a docstring naming the attacker and the asset. The only uncovered line of code is the `if __name__ == "__main__":` block (`app.py` lines 527–538), which just calls `app.run()` to start the server. I moved its one real decision, HTTPS on or off, into `pick_ssl_context()` so that it is tested.

The build enforces 100% in `.coveragerc`:

```ini
[report]
fail_under = 100
exclude_lines =
    pragma: no cover
    if __name__ == .__main__.:
```

Although at the time of writing this competency this 100% does not mean no bugs. One test is marked `@unittest.expectedFailure` because it found a real bug in Myles’s `seed.py`, where building room counts are doubled (NHSSC says 54 rooms but has 27). The test records the bug without breaking the build, and `unittest` will flag it once it’s fixed.
