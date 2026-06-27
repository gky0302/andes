# Texas2k full-TDS support patch for ANDES

This branch is reserved for the Texas2k full dynamic simulation support work.

The patch developed in the accompanying artifact adds:

- `PSS2A` IEEE dual-input stabilizer support.
- Planning-level `GGOV1` support for Texas2k governor records.
- PSS/E DYR mappings for `PSS2A` and `GGOV1`.
- Texas2k MATPOWER/DYR machine-ID remapping helpers.
- Default `WTDTA1` insertion for wind-chain completeness when `WTARA1`, `WTPTA1`, and `WTTQA1` records are present without a matching `WTDTA1`.

The target workflow is:

```bash
andes prepare -i --nomp --ncpu 1
python tools/texas2k_prepare_dyr_for_matpower.py Texas2k_series25_case1_summerpeak.dyr Texas2k_series25_case1_summerpeak.andes-prepared.dyr
python tools/texas2k_parse_after_prepare.py \
  --case Texas2k_series25_case1_summerpeak.m \
  --dyr Texas2k_series25_case1_summerpeak.andes-prepared.dyr \
  --out texas2k_parse_report.json
python tools/texas2k_run_fulltds.py \
  --case Texas2k_series25_case1_summerpeak.m \
  --dyr Texas2k_series25_case1_summerpeak.andes-prepared.dyr \
  --tf 1.0
```

Validation already completed in the local patch package:

- Texas2k static MATPOWER case parses and solves power flow.
- The original Texas2k DYR records can be remapped into ANDES data structures after the patch.
- The main previously missing records are covered: `PSS2A = 568`, `GGOV1 = 517`.
- Model-level symbolic preparation succeeded for both `PSS2A` and `GGOV1`.

Important limitation: full Texas2k TDS integration should be run on a local/HPC machine after `andes prepare -i --nomp --ncpu 1`; the ChatGPT sandbox is not stable for full ANDES multiprocessing code generation on Texas2k-scale systems.
