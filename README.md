# DATA_STRUCTURE

# Neuropixels Data Structure (Peng Lab, BIH/Charité HPC)

Overview of how raw data, Kilosort outputs and QC reports are organised on the cluster.

| Dataset | Owner | Root path |
| --- | --- | --- |
| In vivo (`Test_user`) | Test_User | `/sc-projects/sc-proj-cc15-ag-peng/Invivo_npx/Test_user` |

Like:
| Dataset | Owner | Root path |
| --- | --- | --- |
| In vivo (`Micr`) | Miguel | `/sc-projects/sc-proj-cc15-ag-peng/Invivo_npx/Micr` |
| Ex vivo (`Yangfan`) | Yangfan | `/sc-projects/sc-proj-cc15-ag-peng/Exvivo_npx/Yangfan` |
| Ex vivo (`Manfredi`) | Manfredi | `/sc-projects/sc-proj-cc15-ag-peng/Exvivo_npx/Manfredi` |
| Ex patch (`Amber`) | Amber | `/sc-projects/sc-proj-cc15-ag-peng/Exvivo_patch/Amber` |

Each dataset has three top-level folders: **raw**, **proc** and **quality_control_report**.

---

## 1. Miguel (in vivo) — `Invivo_npx/Micr/`

```
Invivo_npx/Micr/
├── mc_raw/
│   └── {subject}/                         [e.g. mc020]
│       └── {session}/                     [e.g. mc020_260629]
│           └── {timestamp}/               [e.g. 2026-06-29_17-12-00]
│               ├── video_mc0XX_.../       (behavior camera recording folder)
│               ├── Record Node 101/       (OpenEphys raw output)
│               │   └── [experiment/recording]/
│               │       ├── continuous ephys data (.dat), per probe
│               │       └── Settings.xml
│               ├── cam_trigger_A.time.npy / cam_trigger_A.data.npy
│               ├── cam_trigger_B.time.npy / cam_trigger_B.data.npy
│               ├── bar_force.time.npy / bar_force.data.npy
│               └── {mc0XX-date-time}.tsv  (session event/metadata table)
│
├── mc_proc/
│   └── {subject}/                         [e.g. mc020]
│       └── {session}/                     [e.g. mc020_260627]
│           └── {timestamp}/               [e.g. 2026-06-27_23-12-15]
│               ├── {session}_{HHMMSS}_E{exp}_R{rec}_Probe{A/B/C/D}/
│               │                          [e.g. mc020_260627_231215_E1_R1_ProbeA]
│               │                          (Kilosort output per probe: spike times, cluster info)
│               ├── {mc0XX-date-time}.tsv  (metadata, matches mc_raw)
│               └── experiment_summary.txt (per-experiment run summary, probe status / unit counts)
│
└── mc_quality_control_report/
    └── {session}_{HHMMSS}_report_all/     [e.g. mc020_260627_231215_report_all]
```

### Path mapping (raw → proc → QC), example

```
mc_raw/mc020/mc020_260627/2026-06-27_23-12-15/
mc_proc/mc020/mc020_260627/2026-06-27_23-12-15/mc020_260627_231215_E1_R1_ProbeA/
mc_quality_control_report/mc020_260627_231215_report_all/
```

Notes:

- `mc_proc` has the `{timestamp}` level (same name as in `mc_raw`), so proc mirrors raw.
- `mc_quality_control_report` is **flat**: no subject/session folders, straight to the report folder.

---

## 2. Yangfan (Ex vivo) — `Exvivo_npx/Yangfan/`

```
Exvivo_npx/Yangfan/
├── yp_raw/
│   └── {subject_date}/                    [e.g. yp_260331]
│       └── {timestamp}_s{N}/              [e.g. 2026-03-31_16-17-33_s2]
│           │                              (_s{N} = slice index for that session
│           ├── Record Node 10X/           (OpenEphys raw output)
│           │   └── [experimentN]/
│           │       └── [recordingN]/
│           │           ├── continuous ephys data (.dat), per probe
│           │           └── Settings.xml
│
├── yp_proc/
│   └── {subject_date}/                    [e.g. yp_260331]
│        └── Kilosort output per probe (spike times, cluster info)
│
└── yp_quality_control_report/
    └── QC report folder(s) per session
```

Notes:

- Unlike Miguel’s data there is **no subject/mouse level**: data go `{subject_date}` → `{timestamp}_s{N}`.
- `Record Node 10X` sits directly under the timestamp folder

---

## Quick comparison

|  | Miguel (`Micr`) | Yangfan(`Yp`) |
| --- | --- | --- |
| Root | `Invivo_npx/` | `exvivo_npx/` |
| Raw hierarchy | subject → session → timestamp | subject_date → timestamp_s{N} |
| Proc hierarchy | subject → session → timestamp → probe folder | subject_date → timestamp_s{N} |
| QC hierarchy | flat: `{session}_{HHMMSS}_report_all/` | flat per session |
| Record node | `Record Node 101` | `Record Node 10X` |
