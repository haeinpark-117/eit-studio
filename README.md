# EIT Studio

**Bring your own sentence set. Administration, segmentation, transcription and WER scoring for elicited imitation research.**

EIT Studio is a browser-based tool for researchers who administer elicited imitation tasks (EITs)
and need to transcribe and score the resulting oral responses. Speech recognition runs entirely
inside the browser via [transformers.js](https://github.com/huggingface/transformers.js), so audio
files are never uploaded to a server.

**Live site:** https://haeinpark-117.github.io/eit-studio/

---

## What it does

1. **Administer.** Present a sentence set on a fixed schedule and record one response file per
   item. Three practice items precede the scored set. The microphone and the browser storage are
   tested before a session begins, and the session stops immediately if the microphone is lost,
   the audio context is interrupted, or the tab is backgrounded.
2. **Segment.** Split one continuous recording of a whole session into one file per item.
   Boundaries are proposed from a timing grid (stimulus audio or syllable counts) or from the
   silences, and can be adjusted by hand.
3. **Target sentence set.** Paste or upload the target sentences for your own instrument as
   `number, sentence, syllables` — or `sentence, syllables`, in which case items are numbered in
   order. Tab-separated input is accepted when pasted. The 30-item English EIT of
   Ortega et al. (2002) is included as a preset.
4. **Scoring rules.** Configure text normalization before comparison: number-to-word expansion,
   contraction expansion (possessive `'s` is preserved), scoring a self-correction on its final
   attempt, and a user-editable list of lexical variants in `from => to` form. The self-correction
   rule fires only where a stretch of two or more words is repeated *immediately* — a phrase that
   recurs later in the sentence is left alone, since at a distance that is ordinarily syntax rather
   than a disfluency — and never where the repeated stretch also occurs in the target sentence. Rule sets can be
   exported and imported as JSON so that the exact scoring configuration can be archived with a
   study or shared with collaborators.
5. **Recording files.** Drop in the response audio. Files named `participantID_itemNN.ext` are
   matched automatically; anything unmatched can be assigned manually in a table.
6. **Review.** Any result row opens to show the recording, the target, and an editable copy of
   the transcription. A correction never overwrites the automatic output: both are exported, with
   both word error rates and a `reviewed` marker. Corrections are kept in the browser and are
   reapplied to a later run of the same files.

Output includes, per item, the target and the transcription with a word-level alignment, word
error rate (WER), accuracy, and the model's mean token confidence (reported both as a probability
and as the raw average log probability). Per-participant means are summarized separately. Results
export as CSV and JSON.

The export separates what was said from what was scored, because WER is not computed on the
transcription as it is displayed. Every item carries `transcription_raw` (what the recognizer
returned, untouched), `transcription_corrected` (a hand correction, empty where none was made),
and `transcription_final` — the normalized string the rate was actually computed on, after the
scoring rules and after any self-correction listed in `self_corrections_removed` has been dropped.
`target_scored` is the target in the same normalized form, so `wer_final` can be recomputed from
the file alone. Every row also carries `tool_version` and `scoring_version`; the latter identifies
the scoring code itself and is incremented whenever a change alters how a score is arrived at, so
two files produced under different behaviour can be told apart even when the settings read the
same. A `status` column distinguishes `scored`, `no_speech`, `failed`, and
`not_attempted`; a file that failed carries no score rather than a score of zero.

## How WER is computed

WER is implemented directly in the page; there is no external dependency for it. Words are aligned
with a standard Levenshtein dynamic-programming matrix and the rate is computed as
`(S + D + I) / N` over the normalized strings, where `N` is the number of words in the normalized
target. This is the same definition used by the Python library
[`jiwer`](https://github.com/jitsi/jiwer), and the two agree: over 391 randomly generated
reference–hypothesis pairs the WER values are identical to six decimal places.

Two differences are worth knowing if you intend to recompute scores with `jiwer`:

- **Truncation at 1.0.** `jiwer` returns values above 1.0 when insertions outnumber the target
  words; EIT Studio truncates at 1.0, so a very disfluent response is not distinguished from a
  silent one by the score alone.
- **Tie-breaking in the alignment.** When several minimum-edit paths have the same cost, the two
  implementations may split the same total into different numbers of substitutions, deletions and
  insertions. The total, and therefore the WER, is unaffected; only the alignment shown in the
  table differs.

## Notes on interpretation

- Confidence values are model- and decoding-dependent. They are comparable across items processed
  in the same session, but should not be compared across different Whisper checkpoints or decoding
  strategies (for example, greedy decoding in the browser versus beam search in a server pipeline).
- Automatic transcription does not remove the need for human verification. Reporting a
  human-verified subsample alongside automatically scored data is recommended; the review drawer
  records that subsample in the same export as the automatic output.

## Requirements

A recent desktop browser. WebGPU is used when available and is substantially faster; otherwise the
tool falls back to WASM on the CPU. Availability depends on the browser version *and* the
operating system rather than on the browser alone, so the page tests for a working graphics
adapter rather than assuming one. The model is downloaded once and cached by the browser.

## Interface

English.

## License

The **source code** in this repository is released under the MIT License; see `LICENSE`.

Two other kinds of material are included and are *not* covered by that grant:

- **Stimulus recordings** (`audio/eit-a/`) are the author's own recordings of the Ortega et al.
  (2002) sentence set, reported in Park (under review). They are provided for use with this tool.
- **The sentence set itself** is from Ortega, Iwashita, Norris, and Rabie (2002) and remains with
  its original authors. Nothing here grants rights in it.

## Citation

Park, H. I. (2026). *EIT Studio* [Computer software]. https://haeinpark-117.github.io/eit-studio/

A publication describing the tool and its validation is in preparation; the citation on the site
will be updated when it appears.

## Reference

Ortega, L., Iwashita, N., Norris, J. M., & Rabie, S. (2002). *An investigation of elicited
imitation tasks in crosslinguistic SLA research.* Paper presented at the Second Language Research
Forum, Toronto.
