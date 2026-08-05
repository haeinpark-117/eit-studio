# EIT Studio

**Bring your own sentence set. Automated transcription and WER scoring for elicited imitation research.**

EIT Studio is a browser-based tool for researchers who administer elicited imitation tasks (EITs)
and need to transcribe and score the resulting oral responses. Speech recognition runs entirely
inside the browser via [transformers.js](https://github.com/huggingface/transformers.js), so audio
files are never uploaded to a server.

**Live site:** https://haeinpark-117.github.io/eit-studio/

---

## What it does

1. **Target sentence set.** Paste or upload the target sentences for your own instrument
   (TSV, CSV, or one sentence per line). The 30-item English EIT of Ortega et al. (2002) is
   included as a preset.
2. **Scoring rules.** Configure text normalization before comparison: number-to-word expansion,
   contraction expansion (possessive `'s` is preserved), and a user-editable list of lexical
   variants in `from => to` form. Rule sets can be exported and imported as JSON so that the exact
   scoring configuration can be archived with a study or shared with collaborators.
3. **Recording files.** Drop in the response audio. Files named `participantID_itemNN.ext` are
   matched automatically; anything unmatched can be assigned manually in a table.

Output includes, per item, the target and the Whisper transcription with a word-level alignment,
word error rate (WER), accuracy, and the model's mean token confidence (reported both as a
probability and as the raw average log probability). Per-participant means are summarized
separately. Results can be exported for downstream analysis.

## Notes on interpretation

- WER is computed over word-level minimum-edit alignment as `(S + D + I) / N`, truncated at 1.0,
  following the standard definition used by `jiwer`.
- Confidence values are model- and decoding-dependent. They are comparable across items processed
  in the same session, but should not be compared across different Whisper checkpoints or decoding
  strategies (for example, greedy decoding in the browser versus beam search in a server pipeline).
- Automatic transcription does not remove the need for human verification. Reporting a
  human-verified subsample alongside automatically scored data is recommended.

## Requirements

A recent desktop browser. WebGPU (Chrome, Edge) is used when available and is substantially faster;
otherwise the tool falls back to WASM. The model is downloaded once and cached by the browser.

## Interface

English and Korean.

## Reference

Ortega, L., Iwashita, N., Norris, J. M., & Rabie, S. (2002). *An investigation of elicited
imitation tasks in crosslinguistic SLA research.* Paper presented at the Second Language Research
Forum, Toronto.
