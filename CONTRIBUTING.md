# Contributing to Orphan Compass

Contributions are welcome — this started as a demo project and there's a lot
of room to grow it. See the [Roadmap](README.md#roadmap--whats-next) in the
README for concrete ideas, or open an issue with your own.

## Getting started

1. Fork the repo
2. Open `rare_disease_matcher.ipynb` in Colab or Jupyter and run all cells to
   confirm the baseline works
3. Make your changes
4. Re-run the full notebook top to bottom before submitting a PR, to confirm
   nothing breaks downstream

## Ground rules

- Keep the pipeline free to run — no paid APIs, no required signups. If you
  add an optional paid integration, it must be clearly optional with a free
  fallback.
- Keep medical framing careful: this is a research/educational tool. Avoid
  language that implies diagnostic certainty in code comments, prompts, or
  UI text.
- If you touch the HPO parsing or scoring logic, please note in your PR
  description what test symptom description you used to sanity-check the
  output.

## Reporting issues

Bug reports, unclear documentation, and "this ranked X but I'd expect Y"
observations are all useful — please include the symptom text you used and
the output you got.
