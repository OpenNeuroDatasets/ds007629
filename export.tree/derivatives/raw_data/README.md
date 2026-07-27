# ROAMM Raw Eye-Tracking, EEG, and Behavioral Dataset

## Overview

This dataset contains synchronized eye-tracking, electroencephalography (EEG), and behavioral data collected during the **ReMind task**, a retrospective self-report paradigm for studying the onset and duration of mind wandering during natural reading.

Participants read passages at their own pace and reported episodes of mind wandering. After reporting an episode, they retrospectively selected the words where they believed their mind had started and stopped wandering. These word selections were aligned with gaze timestamps to estimate the onset and offset of each mind-wandering episode with greater temporal precision than a report timestamp alone. Participants also completed reading-comprehension questions.

The task and paradigm are described in:

> Sun, H., Birney, A., Singh, N., Olszko, A., Chen, P., Ke, J., Rosenberg, M. D., & Jangraw, D. C. (2026). *ReMind: A Retrospective Self-Report Paradigm for Studying Mind-Wandering Onset During Reading*. bioRxiv. [https://doi.org/10.64898/2026.05.14.725227](https://doi.org/10.64898/2026.05.14.725227)

The files in `raw_data/` are the original or exported acquisition files. They have not been cleaned, filtered, interpolated, epoched, or otherwise preprocessed.

## Directory structure

```text
raw_data/
├── s10000/
│   ├── eeg/
│   │   ├── MR_s10000_r1.bdf
│   │   ├── MR_s10000_r2.bdf
│   │   ├── MR_s10000_r3.bdf
│   │   ├── MR_s10000_r4.bdf
│   │   └── MR_s10000_r5.bdf
│   ├── eye/
│   │   ├── s000_r1_<date_time>.asc
│   │   ├── s000_r2_<date_time>.asc
│   │   ├── s000_r3_<date_time>.asc
│   │   ├── s000_r4_<date_time>.asc
│   │   └── s000_r5_<date_time>.asc
│   ├── FlowSheet_MindlessReading_s10000.docx
│   └── log/
│       ├── 10000_R1_MindlessReading_<date_time>.csv
│       ├── 10000_R2_MindlessReading_<date_time>.csv
│       ├── 10000_R3_MindlessReading_<date_time>.csv
│       ├── 10000_R4_MindlessReading_<date_time>.csv
│       └── 10000_R5_MindlessReading_<date_time>.csv
├── s10001/
│   └── ...
└── ...
```

- Each `s#####` directory represents one participant.
- `r1` through `r5` identify the five separate task runs completed by each participant.
- EEG and flowsheet filenames use the full participant ID, such as `s10014`.
- Eye-tracking filenames use a shortened three-digit EyeLink ID. For example, participant `s10014` appears as `s014` in the ASC filenames.
- Behavioral-log filenames omit the leading `s`, such as `10014_R1_...csv`.
- Eye-tracking timestamps use underscore-separated fields, while behavioral-log timestamps use a format such as `2023-11-29_09h29.06.045`.
- Files belonging to the same participant and run can be linked using the participant ID and run number in their filenames.

## Contents of each participant directory

### `eye/` — eye-tracking data

The eye-tracking recordings were collected binocularly at 1000 Hz using an SR Research EyeLink 1000 Plus. The distributed EyeLink ASCII (`.asc`) files were exported from the original EyeLink EDF recordings using EDFConverter. An ASC file is a plain-text, time-ordered record that can include:

- sample-level gaze data: EyeLink timestamp, horizontal gaze position, vertical gaze position, and pupil measurement;
- left- and right-eye measurements;
- recording-period markers (`START` and `END`);
- task messages (`MSG`) sent by the stimulus computer, including reading and page-related messages;
- fixation events (`EFIX`): eye, start time, end time, duration, average gaze position, and average pupil measurement;
- saccade events (`ESACC`): eye, start time, end time, duration, start and end positions, amplitude in degrees, and peak velocity; and
- blink events (`EBLINK`): eye, start time, end time, and duration.

EyeLink timestamps are expressed in milliseconds. Missing gaze or pupil samples may appear as nonnumeric placeholders in the ASC text. EyeLink automatically identified fixations, saccades, and blinks using its event-detection settings; the corresponding event records are included in the ASC files.

### `eeg/` — EEG data

The EEG recordings are BioSemi Data Format (`.bdf`) files produced by the BioSemi acquisition system. Each file contains a continuous recording for one task run, including:

- signals from the 64-channel BioSemi scalp-electrode montage;
- channel labels and acquisition metadata stored in the BDF header;
- the recording’s native sampling rate; and
- the BioSemi status/trigger stream used to mark task events and align EEG with the behavioral task.

The task-event codes documented in the accompanying preprocessing repository are:

| Code | Event |
|---:|---|
| 1 | Reading started, after instructions and eye-tracker calibration |
| 10 | A new reading page was displayed |
| 30 | Reading page ended |
| 40 | Mind-wandering report |
| 5 | Comprehension questions |

The values above describe acquisition markers. Later analysis code also derives labels such as `MW_onset`, `MW_offset`, `self_report`, `control_onset`, and `control_sr`; those derived labels are not additional raw BDF channels.

The raw BDF files should not be assumed to have the 256 Hz sampling rate used by the preprocessing pipeline. That pipeline resamples the recordings to 256 Hz, applies an average reference and a 0.5–50 Hz band-pass filter, detects and interpolates noisy channels, and performs ICA-based artifact removal. Those operations are not applied to the files distributed here.

### `log/` — behavioral task logs

The log directory contains PsychoPy CSV output from the reading task. These files provide trial- and page-level task information used to interpret and synchronize the eye-tracking and EEG recordings. Depending on the run and task version, columns can include:

- participant, session, run, and timing metadata;
- reading passage and page identifiers;
- page-onset and reading-duration information;
- keyboard responses and response times;
- comprehension-question answers;
- mind-wandering reports, including the retrospectively selected onset and offset words;
- slider ratings;
- experimental condition or implanted-error information; and
- mouse-click positions.

PsychoPy CSV files often contain many columns, with values populated only on the rows where the corresponding task component occurred.

### `FlowSheet_MindlessReading_s#####.docx` — session documentation

The Microsoft Word flowsheet at the root of each participant directory contains the experimenter’s run/session record. It provides contextual information such as run completion, acquisition notes, interruptions, equipment or signal-quality issues, and protocol deviations when recorded.
