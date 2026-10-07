# SPEC.md: "The Human How"; Exact technical requirements, environment setup, implementation details, etc.

## Execution Description

Runs seven ROCm stack probes and emits one CSV row per tool. kernel_version: expected uname -r prefix, published as the kernel mismatch count. driver_version: compared with the amdgpu modinfo version field for driver compliance. When that field is absent, driver compliance is na (in-tree amdgpu has no version). Sweep dimensions: kernel_version, driver_version, rocm_version, required_packages, permissions_check, output_format.

## Parameters

| Parameter | CLI Flag | Tested Values | Default | Description |
| --- | --- | --- | --- | --- |
| kernel_version | `--kernel-version` | smoke=7.0.0, baseline=7.0.0, extended=7.0.0 | 7.0.0 | From Parameter list; see Execution Description With Parameters. |
| driver_version | `--driver-version` | smoke=6, baseline=6, extended=6 | 6 | From Parameter list; see Execution Description With Parameters. |
| rocm_version | `--rocm-version` | smoke=7.14, baseline=7.14, extended=7.14 | 7.14 | From Parameter list; see Execution Description With Parameters. |
| required_packages | `--required-packages` | smoke=rocm-core,rocm-smi, baseline=rocm-core,rocm-smi, extended=rocm-core,rocm-smi | rocm-core,rocm-smi | From Parameter list; see Execution Description With Parameters. |
| permissions_check | `--permissions-check` | smoke=true, baseline=true, extended=true | true | From Parameter list; see Execution Description With Parameters. |
| output_format | `--output-format` | smoke=csv, baseline=csv, extended=csv | csv | From Parameter list; see Execution Description With Parameters. |

## Invocation

```bash
Run rvs --version, rocminfo, rocm-smi, amd-smi static, uname -a, modinfo amdgpu, hipcc --version, and dpkg-query
```

## Raw Output Format

CSV with one row per tool probe, plus a transcript per command (rvs.txt, rocminfo.txt, rocm-smi.txt, amd-smi.txt, uname.txt, modinfo.txt, hipcc.txt)

sample_index,check_name,rocm_package_mismatch_count,kernel_driver_mismatch_count,kernel_mismatch_count,driver_mismatch_count,driver_version_available,driver_compliance_percent,permission_error_count
0,rocminfo,0,0,0,0,1,100,0

## Metrics

- **#1: ROCm package mismatch count** — stored as `rocm_package_mismatch_count`.
- **#2: Kernel mismatch count** — stored as `kernel_driver_mismatch_count`.
- **#3: Driver compliance percent** — stored as `driver_compliance_percent`.
- **#4: Permission error count** — stored as `permission_error_count`.

## Framework

Runs seven ROCm stack probes and emits one CSV row per tool. kernel_version: expected uname -r prefix, published as the kernel mismatch count. driver_version: compared with the amdgpu modinfo version field for driver compliance.

## Installation and Execution Summary

Run rvs --version, rocminfo, rocm-smi, amd-smi static, uname -a, modinfo amdgpu, hipcc --version, and dpkg-query, then compare package presence, the kernel release, driver compliance when amdgpu has a version, and /dev/kfd plus /dev/dri/renderD128 permissions to yaml expectations, to measure ROCm stack compliance

## Platform Portability

- **AMD (primary):** ```bash
Run rvs --version, rocminfo, rocm-smi, amd-smi static, uname -a, modinfo amdgpu, hipcc --version, and dpkg-query
```
- **NVIDIA:** Primary target is AMD ROCm. NVIDIA notes in this section are reference only and are not the execution path.

## Model Context Protocols

- **Active:** None

## Execution-Loop Validation Contract

EXECUTION CHAIN: `run_benchmark.sh` ➔ raw output ➔ `scripts/parse_results.py` ➔ `results/benchmark.db` ➔ `scripts/validate_results.py`

This benchmark uses a lightweight, SQLite-integrated execution loop for result validation. All validation is performed by `scripts/validate_results.py`.

### Validation script usage

```bash
export BENCHMARK_PYTHON=/usr/bin/python3.13  # optional; select the installed interpreter

# After a live run:
".venv/bin/python" scripts/validate_results.py --db results/benchmark.db

# CI / no-GPU path (seeds fixture and validates it):
".venv/bin/python" scripts/validate_results.py --seed-fixture --quiet

# Override DB path via environment variable:
BENCHMARK_DB=tests/fixtures/benchmark.db \
  ".venv/bin/python" scripts/validate_results.py
```

### Run artifact contract

CSV with one row per tool probe, plus a transcript per command (rvs.txt, rocminfo.txt, rocm-smi.txt, amd-smi.txt, uname.txt, modinfo.txt, hipcc.txt)

sample_index,check_name,rocm_package_mismatch_count,kernel_driver_mismatch_count,kernel_mismatch_count,driver_mismatch_count,driver_version_available,driver_compliance_percent,permission_error_count
0,rocminfo,0,0,0,0,1,100,0

```bash
bash run_benchmark.sh --help
bash run_benchmark.sh --profile smoke --validate
bash run_benchmark.sh --profile baseline --validate
bash run_benchmark.sh --profile extended --validate
```
`run_benchmark.sh --help` prints usage and exits. The harness calls `scripts/ensure_setup.sh` when `.setup_state` is absent.

### Required integrity checks (built into `validate_results.py`)

1. Latest run exists and `runs.status = 'ok'`.
2. `run.error_message` is NULL.
3. `started_at` and `finished_at` are valid ISO-8601 UTC strings.
4. All required aggregate metrics in `runs` are non-NULL and finite.
5. All required aggregate metrics are physically sensible (positive values). Runs seven ROCm stack probes and emits one CSV row per tool. kernel_version: expected uname -r prefix, published as the kernel mismatch count. driver_version: compared with the amdgpu modinfo version field for driver compliance.
6. At least 2 sample rows exist for the latest `run_id` (sweep coverage).
7. No sample has `status = 'error'`.
8. Runs seven ROCm stack probes and emits one CSV row per tool. kernel_version: expected uname -r prefix, published as the kernel mismatch count. driver_version: compared with the amdgpu modinfo version field for driver compliance.

### Baseline / Threshold configuration (`config/benchmark_config.yaml`)

Expected ranges and gates live in `config/benchmark_config.yaml` under `baselines:` or `thresholds:`. To update them, edit that file — never edit validation code directly.

Threshold key suffixes encode comparison direction when `thresholds:` is present: `_min` → observed value must be ≥ threshold. `_max` → observed value must be ≤ threshold. Informational `baselines:` ranges are not pass/fail gates.
