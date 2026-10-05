# Spec-Driven Testing

A pattern for testing data-heavy planning engines: describe each scenario as a small JSON spec, generate the input datasets automatically, and check results with tolerances instead of exact matches.

**Live page:** https://josephsamuel2022.github.io/spec-driven-testing/

> This repo describes a general technique using illustrative values. It contains no proprietary code, data or screenshots from any employer.

## The problem

Planning and optimization engines take large tables in and return large tables out. Teams often test them with hand-built binary fixtures. Those files can't be reviewed in a pull request, they break whenever a schema changes, and each new one takes hours to build. As a result, the regression tests that matter most rarely get written.

## The approach

```
Spec (JSON) -> Generate datasets -> Run engine -> Assert with tolerances -> Replay in CI
```

1. **Spec:** a scenario is written as JSON: inputs, rules and expected outcomes.
2. **Generate:** datasets (for example Parquet files) are built from the spec, so a schema change means updating one generator instead of hundreds of fixtures.
3. **Run:** the engine executes against the generated data.
4. **Assert:** outputs are compared to expectations using absolute or relative tolerances, plus constraint checks.
5. **Replay:** validated scenarios re-run in CI on every change, so each past fix becomes a permanent guard.

## Example spec

```json
{
  "scenario": "low-stock-reorder",
  "inputs": {
    "items": [{ "id": "A1", "onHand": 40, "demandPerWeek": 90 }],
    "leadTimeWeeks": 2
  },
  "expect": [
    { "field": "orderQty", "value": 120, "tolerance": { "abs": 2 } },
    { "field": "serviceLevel", "value": 0.95, "tolerance": { "rel": 0.01 } },
    { "field": "stockoutDays", "value": 0, "tolerance": { "abs": 0 } }
  ]
}
```

Quantities can allow a small absolute margin, ratios a relative one, and counts none at all. Constraints (such as "never order below zero") sit beside the numbers, so a run can fail on a broken rule even when every number is in range.

## Results

| Outcome | Result |
| --- | --- |
| Integration-test authoring time | Hours reduced to minutes |
| Reviewable scenario specs | 100+ |
| Validated scenarios replayed to detect regressions | 300+ |

## Why tolerances

Exact matching makes tests fail on harmless rounding or row-ordering differences, so people stop trusting them. Tolerances absorb that noise while still failing on real behavior changes.

## Tech

Java, JSON, Parquet, Gradle CI. The pattern itself is language-agnostic and fits any engine that turns tabular data into tabular results.

## Repo contents

- `index.html`: the single-page site, including an interactive tolerance-assertion demo
- `README.md`: this file

## Run locally

Open `index.html` in a browser. No build step or dependencies are needed.

## Author

Joseph Samuel M
[GitHub](https://github.com/JosephSamuel2022) | [LinkedIn](https://linkedin.com/in/joseph-samuel-m-71a3b320a)
