# Lander Optimizer Engine

This engine is orchestrated by Claude Code using MCP tools. The files here define
the procedures, schemas, and validation logic that Claude follows during each cycle.

## Files

- `cycle-procedure.md` - Step-by-step cycle execution procedure
- `variation-strategy.md` - How to generate meaningful variations
- `pre-deploy-checks.md` - Validation gates before any deploy
- `stats-analysis.md` - Statistical significance and winner determination
- `grading-procedure.md` - Human grading workflow via Telegram

## How to Run a Cycle

Tell Claude: "Run an optimization cycle for [vertical]"

Claude will follow the cycle-procedure.md steps using MCP tools.
