# Open Register

Open Register is a privacy-preserving interaction analysis instrument for measuring stability, volatility, and regime transitions in long-form conversational exchanges. It accepts user-supplied transcript data in multiple formats and performs structural analysis entirely in the browser.

Open Register does not analyze semantic content, sentiment, or meaning. All measurements are derived from non-semantic structural properties of the interaction itself — specifically response length and its variation over time — making the instrument suitable for sensitive or private conversational data.

## Privacy

**Your file is processed entirely in your browser. No data is transmitted, logged, or stored.**

Open Register is implemented as a static single-file HTML application. There is no server-side processing, no upload endpoint, and no analytics. When you close the tab, the data is gone.

## Supported Formats

Open Register accepts transcripts in the following formats. Format detection is automatic on upload.

**CSV** — any source. Column mapping (turn order, speaker, magnitude) is auto-detected and presented for confirmation before analysis runs. Any numeric column can serve as the magnitude signal; the instrument defaults to `tokens_est` if present, followed by `tokens`, `words`, `chars`, and `length` in that order.

**JSON** — standard chat export format. The following structures are supported:
- Arrays of `{role, content}` message objects
- Objects with a `messages` array of the above
- Claude conversation exports (`chat_messages` with `sender`/`text` fields)
- ChatGPT conversation exports (nested `mapping` tree structure)

System messages, tool calls, and empty turns are excluded automatically. All other roles are normalized to `model` or `human`.

**Markdown and plain text** — configurable speaker markers. After upload, you specify the marker strings that identify speaker turns in your transcript (e.g., `You said:` / `Assistant:`). A detection step shows the turn count before you commit to analysis.

### Expected CSV structure

For CSV files, the instrument expects at minimum one numeric column representing response magnitude. A typical well-formed input looks like:

```
turn,speaker,tokens_est
1,model,212
2,human,34
3,model,189
```

Column names do not need to match exactly — the mapping step allows manual correction if auto-detection selects the wrong column.

### JSON format note

Most major AI platform exports follow the `role`/`content` convention or a close variant. If your export does not match any of the supported structures, converting to CSV first is recommended. The expected CSV structure is described above.

## What Open Register Measures

Open Register computes rolling statistics over conversational turns to identify:

1. Interaction volatility (response length variance)
2. Boundedness (whether variability remains constrained)
3. Persistence (whether stability is sustained across time)
4. Time-to-stability (the point at which a stable regime emerges, if at all)

Stability is assessed using z-score deviation from the rolling mean, making the threshold scale-invariant across datasets with different response length distributions. A turn is considered stable if its z-score remains within the configured threshold for a minimum number of consecutive turns.

## Analysis Tabs

**Overview** — scatter plot of turn-level magnitude over time, with configurable rolling mean and optional variance band. Scope can be restricted to model turns only or expanded to all turns. A stability hint is shown using default parameters; full control is available in the Stability Analysis tab.

**Stability Analysis** — full regime detection with configurable z-score threshold and persistence requirement. Stable turns are highlighted in the scatter plot and stable regions are shaded. Detected segments are listed with their turn ranges and lengths.

**Perturbation Testing** — robustness tests for detected structure. Two perturbation types are available:

- *Temporal scramble* — preserves the distribution of values but shuffles turn order within windows of configurable size. Tests whether any detected structural pattern depends on temporal sequence or is an artifact of value distribution alone.

- *Metric noise injection* — adds gaussian noise scaled to a multiple of the observed standard deviation, while preserving turn order. Tests sensitivity of detected structure to measurement precision.

Original and perturbed views are shown side by side for direct comparison.

## What Open Register Is and Is Not

Open Register is an instrument, not a benchmark or evaluation score. It is designed to:

1. Accept user-supplied conversational data in common export formats
2. Observe interaction-level behavior across scale
3. Detect regime transitions (exploratory → convergent → unstable)
4. Test the robustness of detected structure under controlled perturbation

It does not claim to measure: understanding, alignment, intelligence, intent, or agency.

## Relationship to Variance Register

Open Register and Variance Register are companion instruments. Variance Register is a fixed demonstration on curated example archives. Open Register accepts user-supplied data and extends the analysis with perturbation testing. The two instruments share a methodology and visual language but are designed for different use cases.

## Notes

This project was developed by Axiom Drift AI. AI-assisted coding tools were used during implementation, consistent with modern software development practice. All conceptual framing, measurement logic, and evaluation criteria are human-directed.

## Related Work

An exploratory preprint describing the temporal analysis methods underlying this instrument is available at:

*Beyond Content: Temporal Dynamics of Long-Form Human-LLM Interaction*
https://doi.org/10.5281/zenodo.18273459

[Axiom Drift AI](https://axiomdrift.ai)
