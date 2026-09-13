# Contributing to ZKRoadmap

Thanks for your interest in improving ZKRoadmap! This repo is a structured, curated learning
path for Zero-Knowledge Proofs — from math foundations to zkVMs — with free resources
attached to each topic.

Contributions are welcome, especially:

- **Suggesting better free resources** for a topic (a clearer explainer, a more up-to-date
  paper, a better tutorial than what's currently linked).
- **Fixing errors** — technical inaccuracies, broken links, typos.
- **Filling gaps** — a topic that's missing from the roadmap but belongs in the learning path.
- **Improving structure** — better ordering, clearer connections between topics, navigation aids.

## Before you open a pull request

- **Check it fits the roadmap's scope.** This is a ZK-specific learning path, not a general
  cryptography or blockchain resource list. If in doubt, open an issue first to discuss.
- **Prefer free, high-quality resources.** Papers, blog posts, or lecture notes that are
  genuinely useful for learning the specific topic — not just any link that mentions it.
- **Keep the existing file structure.** Each topic lives in its own `.md` file. If you're
  adding a resource to an existing topic, edit that file. If you're proposing a new topic,
  open an issue first so we can agree on where it fits in the sequence.

## Format for adding a resource

Follow the existing style within each file — typically:

```markdown
[Resource Title](https://link-to-resource) — short description of the resource and why it's useful.
```

- Capitalize the description and end it with a period.
- Keep descriptions short (one sentence) but specific about what makes the resource worth
  including.
- If it's replacing an existing link (e.g. a broken one), remove the old entry.

## Opening a pull request

1. Fork the repo.
2. Create a branch for your change (e.g. `add-halo2-resource`).
3. Make your edit in the relevant `.md` file.
4. Commit with a clear message describing what changed and why.
5. Open a pull request with a short description of the change and, if relevant, why the
   resource or fix is worth including.

## Reporting issues

If you spot a broken link, a factual error, or think a topic is missing, feel free to open
an issue instead of a pull request — that works too, especially if you're not sure exactly
how it should be fixed.

## Code of conduct

Be respectful and constructive. This is a learning resource meant to help people — keep
contributions and discussion in that spirit.
