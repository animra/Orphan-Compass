# 🧭 Orphan Compass

**Phenotype-driven rare disease candidate matching - free, local, and grounded in a real clinical ontology.**

Describe symptoms in plain English → get ranked candidate rare diseases, matched against the same phenotype vocabulary used by clinical geneticists, with a local LLM explaining the evidence. No API keys. No cost. No black-box guessing.

> ⚠️ **This is a research/educational tool, not a diagnostic device.** It does not replace a physician or clinical geneticist. See [Limitations](#limitations--disclaimer).

---

## Why this exists

Patients with rare diseases wait years for a correct diagnosis on average, largely because their symptoms don't map cleanly onto the common conditions most clinicians see day to day. Most "AI symptom checker" projects just ask an LLM to free-associate a diagnosis from text — which hallucinates confidently and isn't grounded in anything verifiable.

Orphan Compass instead uses a technique from real clinical bioinformatics — **phenotype-based differential ranking** — and only brings in an LLM at the very end, to *explain* a shortlist that was already computed from real data. The LLM narrates evidence; it doesn't invent it.

## How it works

```
Free-text symptoms
      │
      ▼
1. Semantic matching → standardized HPO phenotype terms
   (sentence-transformers embedding similarity)
      │
      ▼
2. Disease ranking → information-content weighting
   (rare/specific symptoms score higher than common ones,
    same principle used by clinical tools like Phenomizer)
      │
      ▼
3. Local LLM explains the top candidates,
   citing only the matched phenotypes
```

## Data & credits

This project is built entirely on public data and open-weight models. No part of the underlying ontology, annotation data, or model weights was created by this project — full credit below.

**Human Phenotype Ontology (HPO)**
The phenotype vocabulary and disease-phenotype annotations come from the [Human Phenotype Ontology](https://hpo.jax.org/), developed by the Monarch Initiative in collaboration with the Open Biomedical Ontologies Foundry, and hosted by the Jackson Laboratory. HPO is free to use with attribution; this project does not modify the ontology itself, only reads it. If you build on this project for anything beyond casual use, please cite the HPO project directly per their [citation guidance](https://obophenotype.github.io/human-phenotype-ontology/community/cite/) — the standard reference is:

> Köhler S, et al. *The Human Phenotype Ontology in 2021.* Nucleic Acids Research, 2021.

**Embedding model**: [`all-MiniLM-L6-v2`](https://huggingface.co/sentence-transformers/all-MiniLM-L6-v2), from the [sentence-transformers](https://www.sbert.net/) project (UKP Lab).

**Language model**: [`Qwen2.5-0.5B-Instruct`](https://huggingface.co/Qwen/Qwen2.5-0.5B-Instruct), open-weight, released by Alibaba's Qwen team.

## Quick start

Open `rare_disease_matcher.ipynb` in [Google Colab](https://colab.research.google.com/) or Jupyter, and run all cells top to bottom. First run downloads the HPO dataset (~150MB) and model weights — after that, everything runs locally with no network calls.

```python
diagnose("recurrent bone fractures, blue-tinted sclera, hearing loss, short stature")
```

```
Matched phenotypes:
  - 'recurrent bone fractures' -> Bone fragility (HP:0003818, similarity 0.71)
  - 'blue-tinted sclera' -> Blue sclerae (HP:0000592, similarity 0.83)
  - 'hearing loss' -> Hearing impairment (HP:0000365, similarity 0.79)
  - 'short stature' -> Short stature (HP:0004322, similarity 0.91)

Top candidate diseases:
  8.42  Osteogenesis imperfecta type I — matched: Bone fragility, Blue sclerae, Hearing impairment
  ...
```

## Repo structure

```
orphan-compass/
├── rare_disease_matcher.ipynb   # full pipeline, runnable end-to-end
├── README.md
└── (hp.obo, phenotype.hpoa downloaded at runtime — not checked into repo)
```

## Roadmap / what's next

- [ ] **LLM-based symptom extraction** — replace the naive comma-split with the local LLM pulling out phenotype phrases, so multi-clause descriptions ("started walking late and never really talked much") are captured properly
- [ ] **Streamlit/Gradio front end** — turn `diagnose()` into a shareable web demo
- [ ] **Evaluation harness** — HPO ships known disease-phenotype pairs; hold some out and measure recall@k for the ranking algorithm
- [ ] **Use frequency/onset data** — `phenotype.hpoa` includes columns this project doesn't use yet (symptom frequency within a disease, typical onset age) that could sharpen the ranking
- [ ] **Multi-term extraction per phrase** — currently each phrase maps to one HPO term; some phrases genuinely describe two phenotypes at once
- [ ] **Larger local LLM option** — add a config flag for `Qwen2.5-1.5B-Instruct` or `Phi-3-mini` for users with GPU access, for richer explanations
- [ ] **Explainability report export** — generate a shareable PDF/markdown summary of a query + results, useful for bringing to an actual clinician

Contributions welcome on any of the above — open an issue or PR.

## Limitations & disclaimer

- This tool is for research and educational purposes only. It is **not** a diagnostic tool and must not be used to make real medical decisions.
- Rare disease diagnosis depends on far more than symptom text — family history, lab values, imaging, and genetic testing all matter and are outside this project's scope.
- The semantic matching step is imperfect; ambiguous or colloquial phrasing may map to the wrong HPO term.
- If you or someone you know is dealing with an undiagnosed condition, the right next step is a clinical geneticist or a genetics clinic — not this notebook.

## License

MIT for the code in this repository. The HPO dataset itself is separately licensed by the HPO Consortium (free to use with attribution, not to be redistributed in modified form) — see [HPO's license page](https://hpo.jax.org/license) for details.
