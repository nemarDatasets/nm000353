# Bonn intracranial EEG segments, sets C, D and E (iEEG-BIDS)

Single-channel intracranial EEG segments from five epilepsy patients, from the data set of:

> Andrzejak RG, Lehnertz K, Mormann F, Rieke C, David P, Elger CE (2001). Indications of nonlinear
> deterministic and finite-dimensional structures in time series of brain electrical activity: Dependence on
> recording region and brain state. *Phys. Rev. E* 64, 061907.
> doi:[10.1103/PhysRevE.64.061907](https://doi.org/10.1103/PhysRevE.64.061907)

Source dataset: doi:[10.34810/data490](https://doi.org/10.34810/data490), hdl:[10230/42894](http://hdl.handle.net/10230/42894)
(Repositori Digital de la UPF; mirror of the UPF NTSA download page). The authors ask: "The correct citation is
Phys. Rev. E, 64, 061907. No page numbers should be given."

This BIDS dataset is a lossless re-packaging of the intracranial sets only. Every sample is identical to the
published text files; nothing was filtered, resampled, re-referenced or rescaled during conversion.

## Which sets are included, and why

The source has five sets (A-E) of 100 segments each:

| Set | Source file | Recording | In this dataset |
|---|---|---|---|
| A | `Z.zip` | surface (scalp) EEG, five healthy volunteers, awake, eyes open | **no**: scalp EEG from healthy volunteers, not intracranial |
| B | `O.zip` | surface (scalp) EEG, five healthy volunteers, awake, eyes closed | **no**: scalp EEG from healthy volunteers, not intracranial |
| C | `N.zip` | intracranial, seizure-free interval, hippocampal formation of the opposite hemisphere | yes (`task-interictal_acq-setC`) |
| D | `F.zip` | intracranial, seizure-free interval, within the epileptogenic zone | yes (`task-interictal_acq-setD`) |
| E | `S.zip` | intracranial, seizure activity, all recording sites exhibiting ictal activity | yes (`task-ictal_acq-setE`) |

This is an intracranial EEG (iEEG) release. Sets A and B are scalp recordings from healthy volunteers, so they
are not redistributed here. They remain available from the source record.

## Recordings (paper, Sec. II A)

- "Sets C, D, and E originated from our EEG archive of presurgical diagnosis. For the present study EEGs from
  five patients were selected, all of whom had achieved complete seizure control after resection of one of the
  hippocampal formations, which was therefore correctly diagnosed to be the epileptogenic zone."
- Depth electrodes were implanted symmetrically into the hippocampal formations. Segments of sets C and D were
  taken from all contacts of the respective depth electrode. Strip electrodes were implanted onto the lateral
  and basal regions of the neocortex. Segments of set E were taken from contacts of all depicted electrodes
  (paper, Fig. 2).
- Segments (23.6 s each) were selected and cut out of continuous multichannel EEG recordings after visual
  inspection for artifacts, e.g. muscle activity or eye movements. They also had to satisfy a weak-stationarity
  criterion (paper, Sec. II B).
- "All EEG signals were recorded with the same 128-channel amplifier system, using an average common reference
  [omitting electrodes containing pathological activity (C, D, and E) ...]. After 12 bit analog-to-digital
  conversion, the data were written continuously onto the disk of a data acquisition computer system at a
  sampling rate of 173.61 Hz. Band-pass filter settings were 0.53-40 Hz (12 dB/oct.)."
- Funding: Deutsche Forschungsgemeinschaft. The paper reports no ethics statement.

## Filtering: the paper and the download page disagree

- Paper: "Band-pass filter settings were 0.53-40 Hz (12 dB/oct.)."
- UPF download page (captured 2026-10-06): "The time series you can download here are not filtered. The
  application of a low-pass filter of 40 Hz, as described in the manuscript, is regarded as the first step of
  analysis and therefore not carried out for the downloadable time series."

Both statements are reproduced here; the BIDS sidecars therefore record `SoftwareFilters` and
`HardwareFilters` as `n/a` and quote both in `FilterNotes`. No filtering was done during conversion.

## Content and verified properties

| | |
|---|---|
| Segments | 300 (100 per set C, D, E) |
| Channels per run | 1 (`x`) |
| Samples per segment | **4097** in every file (the paper and the download page say 4096) |
| Sampling rate | 173.61 Hz (duration 4097 / 173.61 = 23.599 s) |
| Values | integers, -1885 to 2047 |
| Patients | 5 (pooled; segment-to-patient mapping not provided) |

The values are integers in the range of a signed 12-bit converter (-2048 to 2047). Three segments reach 2047,
the converter maximum, in 72 samples in total (set D: 1 file; set E: 2 files). These samples are probably
clipped. Per-file counts are in `sub-pooled_scans.tsv` (`n_samples_at_2047`).

## Units

The source does not state the physical scale of the integer values (for example, microvolts per unit). The
paper's Fig. 3 caption says intracranial amplitudes are "around some 100 µV" and seizure activity "can exceed
1000 µV", which is consistent with roughly 1 µV per unit. That is not a documented calibration. Channel `units`
are therefore `n/a` (in `channels.tsv` and in the BrainVision header), and the values are the source integers
unchanged.

## BIDS layout and source-to-BIDS mapping

The source states: "the signals included in these sets are randomized with regard to the recording contact and
the patient or volunteer. Accordingly, the information which signal corresponds to which recording contact or
patient or volunteer is not available." All runs therefore sit under one pseudo-subject, `sub-pooled`. This is
**not one person**: it pools anonymous segments from five patients. Do not treat segments as independent
subjects.

| Source | BIDS |
|---|---|
| `N.zip` / `N<NNN>.TXT` (set C) | `sub-pooled/ieeg/sub-pooled_task-interictal_acq-setC_run-<NNN>_ieeg.{vhdr,vmrk,eeg}` |
| `F.zip` / `F<NNN>.txt` (set D) | `sub-pooled/ieeg/sub-pooled_task-interictal_acq-setD_run-<NNN>_ieeg.{vhdr,vmrk,eeg}` |
| `S.zip` / `S<NNN>.txt` (set E) | `sub-pooled/ieeg/sub-pooled_task-ictal_acq-setE_run-<NNN>_ieeg.{vhdr,vmrk,eeg}` |
| the single column of each file | channel `x` |
| `N.zip`, `F.zip`, `S.zip`, repository metadata | byte-identical copies in `sourcedata/upf-repositori-10230-42894/` |
| `Z.zip`, `O.zip` (sets A, B, scalp) | not included (see above) |

The run number equals the number in the source file name (001-100). `task-interictal` / `task-ictal` label the
brain state; there was no task. `sub-pooled_scans.tsv` lists for every run the set, source file and its
SHA-256, value range, clipping counts, recording region and brain state. Sidecars are shared by
inheritance:

- `sub-pooled_task-interictal_ieeg.json` and `sub-pooled_task-ictal_ieeg.json`
- `sub-pooled_task-interictal_channels.tsv`: type `SEEG`, because sets C and D come from depth-electrode
  contacts in the hippocampal formation.
- `sub-pooled_task-ictal_channels.tsv`: type `OTHER`, because a set E segment may come from a depth or a strip
  contact and the source does not say which.
- one `..._acq-setX_events.tsv` per set, with a single event spanning the run.

Electrode positions are not available, so there is no `electrodes.tsv`.

## Data format and exactness

The source integers are stored as BrainVision `INT_16` (multiplexed, resolution 1). A separate round-trip check
re-read all 300 files and compared every sample with the source text (see the campaign ledger).

## Privacy

The published files contain only integer samples (no headers, names, dates or identifiers). The source
randomized segments across patients and contacts. Converted headers contain no dates or identifiers.

## License and terms of use

The repository record lists two rights statements (dc.rights), quoted verbatim:

1. "Licensed under a Creative Commons License (CC-BY) 4.0" (https://creativecommons.org/licenses/by/4.0/)
2. "The source codes, data and results on these sites are free of charge for research and education purposes
   only. Any commercial or military use is prohibited. All resources are provided without any expressed or
   implied warranty. In no event the authors of the article or any of their host institutions are liable for
   any damages arising from the use of the software, data or results."

The UPF download page carries the same Legal Agreement. To respect the research-and-education-only condition,
this BIDS release uses the closest standard license, **CC-BY-NC-4.0** (attribution, non-commercial). No
standard license expresses the additional **prohibition of military use**, and it still applies: by using
these data you agree to use them for research and education only, and not for commercial or military
purposes. See `LICENSE`.

## How to cite

Cite Andrzejak et al. (2001), Phys. Rev. E 64, 061907 (doi:10.1103/PhysRevE.64.061907) and the dataset
doi:10.34810/data490. Also cite this BIDS release by its NEMAR identifier.

## Conversion provenance

Converted 2026-10-06 on SDSC Voyager (Kubernetes jobs) by the iEEG-NEMAR campaign (lane G) with
`laneG_convert.py` and `laneG_finalize.py`. Source files were downloaded through the repository's DSpace REST
API, and every bitstream's MD5 matched the repository checksum. See `sourcedata/provenance.json`. Paper
details were taken from the published version deposited at hdl:10230/43637 (repository full-text extraction)
and from the UPF NTSA download page.
