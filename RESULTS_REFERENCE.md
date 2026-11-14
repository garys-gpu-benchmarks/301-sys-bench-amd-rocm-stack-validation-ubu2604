# Results: 301 System Config Verification

**Main goal:** Analyze ROCm software stack consistency against yaml expectations  
**Tools:** Run rvs --version, rocminfo, amd-smi version, amd-smi static, uname -a, modinfo amdgpu, hipcc --version, and dpkg-query

Reference results measured by the maintainer on the system shown in each section (generator TEMPLATE_00_140 · recorded 2026-11-13). Your own runs write `results/RESULTS_ACTUAL.md` in the same format.

> Smoke runs use deliberately small sizes. They check that the workload installs and runs end to end; their numbers are not performance results. Baseline and extended are.

<!-- profile:smoke -->
## smoke

- **System:** 1× AMD Instinct MI300X VF (196288 MB) · INTEL(R) XEON(R) PLATINUM 8568Y+ · Ubuntu 26.04.1 LTS · kernel 7.0.0-38-generic · driver 7.1.9.31600000
- **Run:** 2026-11-13T19:57:26Z · status **PASS** (exit 0)
- **Time (mm:ss):** setup 06:13 · benchmark 00:01 · whole run 06:21
- **Source:** run 301_20261113_195726
- **Command:** `bash run_benchmark.sh --smoke --kernel-version 7.0.0 --required-packages amdrocm-base10.1,amdrocm-amdsmi10.1 --permissions-check true --driver-version 7.1.9 --rocm-version 10.1.0`

| # | Metric | Result |
|---|---|---|
| 1 | ROCm package mismatch count (rocm_package_mismatch_count) | 0.0 |
| 2 | Kernel release mismatch count (kernel_release_mismatch_count) | 0.0 |
| 3 | Driver compliance percent (driver_compliance_percent) | 100.0 |
| 4 | Permission error count (permission_error_count) | 0.0 |
<!-- /profile:smoke -->
