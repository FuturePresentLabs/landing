---
title: "Building Jarvis: A Better Voice Starts Before Training"
date: 2026-09-06T00:00:00-07:00
draft: true
author: "Future Present Labs"
description: "Inside FPL's Jarvis TTS work: a dataset studio for field recordings, selective cleanup, reproducible LoRA training, and the experiments that changed how we evaluate a voice."
categories: ["Engineering"]
tags: ["AI", "speech", "audio", "LoRA", "software", "computing"]
---

We started with a voice we wanted to get right. We ended up building a studio to understand why we weren't getting it right.

Jarvis is FPL's internal text-to-speech project: a custom LoRA adapter built on [VoxCPM2](https://github.com/OpenBMB/VoxCPM), paired with a selected voice reference and a carefully tested inference recipe. It is not a foundation model trained from scratch. Our work is in adapting the voice, preparing the data, evaluating the result, and making the whole process usable.

The goal sounds simple: speech with a consistent character, clear articulation, and a delivery that works in an assistant. In practice, a line could sound excellent on its own and become a different voice when we changed the sentence. A cleaner training clip could produce a less convincing result. A reassuring evaluation score could miss an artifact that was immediately obvious to a listener.

The biggest lesson was that "train it more" was often the wrong next experiment.

## Field Recordings Are Not a Ready-Made Dataset

Our source material included background music, environmental noise, electronic sounds, and changes between recordings. Some of the texture in the voice was intentional. Removing everything that looked irregular was not the same as isolating the speaker.

A long source file could also contain several distinct recordings. Treating that file, or an oversized excerpt from it, as one continuous utterance taught the model a relationship that did not really exist. Punctuation alone couldn't tell us where one recording ended and another began.

We initially worked with split files. That made it easy to lose the surrounding context needed to fix a bad boundary. A clipped consonant, a missing sentence ending, or a transition into another recording could become baked into the dataset.

The better source of truth was the original recording plus a versioned set of decisions about it.

## The Studio: Source Audio, Decisions, and Auditions

Our dataset studio, currently called Tape Sampler, is a Rust-backed editor with a browser interface and a WaveSurfer timeline. It is built around long recordings, editable regions, and an edit decision list rather than a directory of increasingly mysterious WAV files.

Each project keeps its source media and the decisions needed to derive a dataset: start and end times, transcripts, speaker labels, review status, processing settings, notes, and split groups. Import preserves the original media and creates a working copy for editing. Final training clips are exports, not replacements for the originals.

The everyday workflow is deliberately hands-on:

- Drop source media into a project and inspect the waveform.
- Zoom, add regions, and adjust boundaries against the full recording.
- Play through candidates, approve them, reject them, or mark them for review.
- A/B the source against processing presets, with additional adjustments per region.
- Edit transcripts, label speakers, and undo decisions that do not help.
- Export a checked, versioned dataset when the selection is ready.

Keyboard transport, predictable zoom, undo/redo, and one-at-a-time playback turned out to matter. Reviewing audio should not require fighting the interface or opening a music player that decides to continue into an unrelated album.

We also simplified model auditions. Instead of a large matrix of individual files, each candidate can play through the same sequence of lines in one row, with the text visible above it. Raw, processed, and level-matched versions answer different questions without making the listener rebuild the comparison mentally.

## Heuristics That Propose, Not Pretend

The studio uses several layers of evidence to help find useful regions. We do not treat any one score as an automatic certificate of quality.

The lightweight analyzer examines short audio frames, estimates an adaptive noise level, finds active regions, and looks for quieter internal points when a region is too long. It considers local background level, the margin between the candidate and its surroundings, nearby pauses, and a rough high-frequency activity proxy. Those signals produce review priorities and suggestions for light cleanup or a separation preview.

Its "music risk" score is a heuristic for a sustained background bed. It is not a trained music classifier. Likewise, an energy threshold is not speaker identification.

For better timing, we added a separate path using faster-whisper word timestamps and Silero voice activity detection. This was a substantial improvement over estimating sentence boundaries from transcript character counts. The UI's word-timed proposals are review-only; they do not silently replace existing edits.

Our stricter autonomous curation pass combined word timing with existing transcripts and the earlier known-good selection. It then transcribed each proposed cut again in isolation. That last step matters: a transcript can look correct with surrounding context while the exported clip is missing part of the phrase.

In the V3 pass, 39 candidates became 35 clips after four cuts were rejected for transcript disagreement, multiple sentences, or a trailing fragment. The final set contained 85.82 seconds of audio. That is a small adaptation dataset, not evidence of broad generalization, but it gave us a more controlled experiment.

The automation is accessible through JSON endpoints as well as the UI. An agent can inspect suggestions and project state and participate in the workflow. That does not mean the system can autonomously identify every speaker, remove every background source, or decide which voice has the right character.

## Cleanup Is a Per-Clip Decision

We learned this one by making examples worse.

Some separation and denoising variants sounded washed out. They reduced unwanted sound but also weakened the texture we were trying to preserve. We initially questioned the sample rate; listening feedback pointed to the separation in those examples instead. That was not a controlled sample-rate study, so we do not claim sample rate never matters.

Our response was to make processing selective and reversible. The studio can preview Demucs vocal isolation on a selected region with a little surrounding context. That lets us ask whether separation helps this particular clip before committing to it. Smaller-region separation is an option to test, not a proven universal improvement over processing a whole recording.

The current preview path also has a practical limitation: auditioning a Demucs result does not automatically promote it into the exported dataset. Selection and export still need to agree on which source is being used.

For many clips, a lighter chain was the more useful baseline: a high-pass filter for low-frequency rumble, restrained upper-frequency shaping, and controlled output level. The V3 export used a 75 Hz high-pass, a 6.8 kHz low-pass, and a -23 LUFS loudness target before resampling to 16 kHz. It added no new broadband denoising or de-essing.

That dataset contained 17 raw-source takes and 18 existing Demucs vocal takes. We did not run new separation for V3. Both sources remained useful; the lesson was to choose between them, not declare one universally better.

## A Voice Spectrum Is a Starting Point, Not a Wall

We also built a project-level voice profile from selected clips. The profiler calculates short-time FFT spectra, normalizes frame energy so a loud clip does not dominate, and averages the result to estimate useful frequency ranges.

One profile experiment used 73 selected regions. It suggested a gentle starting chain: a 65 Hz high-pass, a 2 dB high-shelf reduction above 6.5 kHz, and an 8.4 kHz low-pass. Broadband denoising stayed off.

That was a separate experimental branch, not the filtering recipe used by the selected V3/V4 targets. Keeping that distinction written down prevents a later successful audition from being attributed to processing it never received.

Nor does an average spectrum isolate a voice by itself. Speech and background sounds overlap in frequency. Consonants, breath, room sound, and music can occupy the same bands. The useful outcome is a conservative preset to audition, not a rule to delete everything outside an estimated vocal range. A contaminated reference set will also produce a contaminated profile.

## Training Less Blindly

Jarvis uses LoRA fine-tuning rather than updating and redistributing the entire base model. Our V3 experiments used rank-32 adapters on the language-model and diffusion components. We preserved checkpoints so that a later run could be compared with the actual earlier listening winner.

V4 continued from the selected V3 adapter for 32 additional low-learning-rate optimizer updates, with a fixed training reference. Its 34 target WAVs were unchanged from V3; the training reference was removed as a target. The split remained 29 training targets and five validation targets.

We also kept related source windows and duplicate transcripts on the same side of the train/validation split. Otherwise, near-identical material can make evaluation look more independent than it is. Grouping helps, but it cannot recover recording identities that were never labeled.

The exports preserve the edit list, processing parameters, source and output hashes, transcript corrections, and split membership. Model weights live separately from training and reference audio. Version control covers the recipe, while appropriately controlled artifact storage holds the media and checkpoints.

That makes it possible to answer a much better question than "Which folder was the good one?": which source, which cut, which transcript, which filter, which adapter, and which generation settings produced this result?

## The Reference Was Part of the Voice

Our best-sounding V3 candidate still changed timbre, tone, and loudness across sentence structures. We compared V3 and V4 both with and without the same fixed reference, rather than attributing every difference to training.

On eight prompts, reference conditioning reduced V3's raw between-clip loudness span from 13.10 to 3.02 LUFS. Speaker-embedding similarity also increased. That pointed to conditioning as an important factor, but it did not settle the artistic question: the reference itself sounded wobbly and washed out.

A later listening comparison selected a different reference, nicknamed "interface." The deployed V4 adapter uses that reference at inference time. It was not the reference used during V4 training, and it was already present among the training targets. This is not a held-out voice-generalization result.

The lesson is simple: stabilizing the wrong reference is not success. A speaker-similarity score cannot choose the voice we want to hear.

## Separate the Model From Everything Around It

One apparent synthesis failure was upstream of synthesis. The input normalizer changed `No. Wait.` into `number Wait.`. In a literal-input control, bypassing normalization restored "No" across three seeds, although one take still omitted "Wait."

That explained part of the failure, not all of it. We did not turn off all normalization in the selected serving recipe; number and abbreviation handling still need deliberate testing.

At the other end of the pipeline, our Forward preset applies a small pitch adjustment, EQ, de-essing, compression, excitation, and loudness normalization. It is part of the finished audition, not part of the training-data cleanup. We retain raw output so processing cannot conceal where an artifact originates.

For comparisons, we also made constant-gain level-matched copies. In the stress set, this reduced the remaining between-clip loudness spread after Forward from 2.42 to 0.04 LUFS. It did not fix changing prosody or noise within an utterance, and it was not promoted as a replacement production preset.

## What Our Metrics Missed

A 36-take stress test covered 12 prompts and three seeds. Transcript and speaker checks were useful, but listening uncovered something they did not measure: a rising harshness or screeching quality during longer output.

We tested processing bypasses and compared one long generation with separately generated sentences. Forward was applied once after the sentences were joined, so the comparison was not simply resetting the audio effects between sentences.

For that example, the measured early-to-late high-frequency rise after Forward dropped from about 6.97 to 2.41 dB. Listening found only a modest improvement. Spectral energy includes speech and sibilants; it is not an isolated noise-floor measurement. We cannot claim sentence generation solved the problem.

We then compared six, twelve, and twenty-four inference steps with the same adapter and reference, across three prompts and two seeds. All 18 raw and processed takes passed the normalized transcript check. Higher steps reduced some processed spectral trends, but did not establish a clear audible fix.

They did cost time. On the MPS machine used for the comparison, the long passages took roughly 41-44 seconds at twelve steps and 68-73 seconds at twenty-four. Six-step controls reused earlier recordings, so we did not invent a fresh six-step timing comparison.

We chose six. Inference steps are synthesis effort, not extra training updates, and generation speed is not the same as speaking speed. More compute needs to earn its place in an interactive system.

## From Audition to an Internal Service

The selected V4 recipe now runs on our internal CUDA worker under the existing `fpl/tts-jarvis` model identifier. The deployment pins the adapter and reference, sets six inference steps, and uses a fresh audio cache so old generated chunks cannot masquerade as the new voice.

In a short, warm, uncached test through our node gateway on an RTX 3060, a 2.72-second reply took about 1.90 seconds as buffered Forward audio. Raw streaming delivered its first audio in about 132 milliseconds and finished in 1.72 seconds. These are smoke-test observations, not service-level guarantees or long-form benchmarks.

Buffered output includes Forward processing at 24 kHz. The existing streaming path returns raw 48 kHz PCM; it does not include the same mastering chain. The transcription check also heard "So" instead of "Sir" in the opening. That ambiguity, the longer-output artifacts, and the difference between streaming and mastered audio remain part of the work.

## Bringing the Studio Into FPL's Internal Cloud

We plan to make the dataset studio available as part of [FPL's internal AI-native cloud platform](/post/ai-native-cloud-internal-access/).

The direction is a connected workflow: source media, editable decisions, processing previews, dataset exports, training jobs, evaluation, and pinned deployments. CPU and GPU workloads both have a role, with an emphasis on running near the data and on infrastructure the organization controls.

The current studio is not yet that multi-user cloud product. It has a server-wide active project, and concurrent editors currently need separate instances. Speaker labels are implemented, but ECAPA enrollment and automatic target-speaker cutting remain future work. Access controls, job isolation, shared-project behavior, and the licensing and rights requirements for broader availability still need to be addressed.

We'll share availability details when that integration is ready. For now, the model and studio remain internal, and the public fine-tune release is still ahead.

## What We Want to Carry Forward

Keep the original recordings. Make every cut and processing decision reversible. Use heuristics to prioritize work, not disguise uncertainty. Compare the complete finished voice as well as the raw signal. Keep the previous listening winner before changing the next variable.

Above all, don't confuse a model checkpoint with the system that makes it useful.

We're excited to bring more of this workflow into the tools we use for manufacturing, computing, and software development. Follow [Future Present Labs on Hugging Face](https://huggingface.co/FuturePresentLabs) for future model updates, or [contact us](/contact/) to discuss the studio and our internal-cloud direction.
