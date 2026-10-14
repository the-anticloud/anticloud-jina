# Result Register — JINA

Every row states the pass condition, the exact command, the observed value, and
the SHA3-256 of **that check's own evidence file** in `04_Evidence/`. Recompute
any hash to verify the row.

| Result | Pass Condition | Command | Observed | Status | SHA3-256 of evidence |
|---|---|---|---|---|---|
| 01_loc_files | status==PASS | python tools/run_bench.py --only 01_loc_files | {"ceilings": {"max_files": 120, "max_lines": 20000, "min_lines": 2000}, "code_lines": 6889, "command": "python tools/run_bench.py --only 01_loc_files" | PASS | `d53b5b85f9170207793adb71d13a15eb290089aa41fdafc4e2a8d071a2070a60` |
| 02_licence | status==PASS | anticloud-ref licence-classify LICENSE && python tools/run_bench.py --only 02_licence | {"a_licences": ["Apache-2.0", "BSD-2-Clause", "BSD-3-Clause", "ISC", "MIT", "MPL-2.0"], "b_licences": ["AGPL", "Commons Clause", "GPL", "LGPL", "SSPL" | PASS | `0fa798a47381b74dca033880274921c06ca80106580ebd6cb5627b060757202d` |
| 03_dependency_scan | status==PASS | anticloud-ref deps-verify requirements.lock | {"command": "anticloud-ref deps-verify requirements.lock", "declared_in_pyproject": ["cryptography"], "forbidden_ml_imports_present": [], "lock_file": | PASS | `7968ef3375c5032a78e718292dd239072e2d35b17fbbb55694a06f946793b4c0` |
| 04_sbom_cyclonedx | status==PASS | python -c "import json;d=json.load(open('sbom.cdx.json'));print(d['bomFormat'],d['specVersion'],len(d['components']))" | {"command": "python -c \"import json;d=json.load(open('sbom.cdx.json'));print(d['bomFormat'],d['specVersion'],len(d['components']))\"", "components":  | PASS | `7292bbdce1a111125f30fed53e61235cce70b203951da1a2f748ed725bf1d2a6` |
| 05_git_health | status==PASS | git status --porcelain && git log --oneline -1 && git fsck --no-progress | {"branch": "main", "command": "git status --porcelain && git log --oneline -1 && git fsck --no-progress", "commits": 16, "gitignore_present": true, "g | PASS | `7bdc6059c254e09eac7eca64760b75fd3060b354b8057e5d346dc636ab683f9a` |
| 06_owasp_llm_top10 | status==PASS | python tools/run_bench.py --only owasp | {"authority": "OWASP Foundation", "command": "python tools/run_bench.py --only owasp", "control_ids": ["LLM01", "LLM02", "LLM03", "LLM04", "LLM05", "L | PASS | `b84548ee1c9bb767a5b4e2d55d491a922d7930d3f5d05f3be4b77c108155dc34` |
| 07_owasp_top10 | status==PASS | python tools/run_bench.py --only owasp | {"authority": "OWASP Foundation", "command": "python tools/run_bench.py --only owasp", "control_ids": ["A01:2021", "A02:2021", "A03:2021", "A05:2021", | PASS | `e5fa844c7fc2ba37de1eb0419b965ad8f6a5babc76b687bbd021bb2f9d49f0d5` |
| 08_soc2_type2 | status==PASS | python tools/run_bench.py --only soc2 | {"authority": "AICPA", "command": "python tools/run_bench.py --only soc2", "control_ids": ["CC6.1", "CC6.2", "CC6.6", "CC6.7", "CC6.8", "CC7.2", "CC8. | PASS | `4d926c2bbc7c02580617c8f15add0c72084a92f7a833b9920ec733d2f4f216d3` |
| 09_nist_ai_rmf | status==PASS | python tools/run_bench.py --only nist | {"authority": "NIST AI RMF", "command": "python tools/run_bench.py --only nist", "control_ids": ["GOVERN-1.1", "GOVERN-2.2", "MAP-1.1", "MAP-3.5", "ME | PASS | `2bc9665532c5ebb065564e0c0304bea26458fc0c24463782d29d705dcb1df548` |
| 10_nist_sp_800_53 | status==PASS | python tools/run_bench.py --only nist | {"authority": "NIST", "command": "python tools/run_bench.py --only nist", "control_ids": ["AC-3(2)", "AC-6(7)", "AU-2", "AU-3", "AU-10", "CM-6", "CP-9 | PASS | `369a8edf4fd96f677b7cf96e32a479e205b97d8d52a17f5cf65645e337a54de7` |
| 11_nist_csf | status==PASS | python tools/run_bench.py --only nist | {"authority": "NIST", "command": "python tools/run_bench.py --only nist", "control_ids": ["GOVERN-1.1", "GOVERN-2.2", "MAP-1.1", "MAP-3.5", "MEASURE-2 | PASS | `21cd69aba150772b15a390ec97a23c96186147abddb444cfb4a870b152116c05` |
| 12_fedramp | status==PASS | python tools/run_bench.py --only fedramp | {"authority": "FedRAMP", "command": "python tools/run_bench.py --only fedramp", "control_ids": ["IA-2", "IA-5", "RA-5", "SA-4", "SA-10", "SA-11", "SC- | PASS | `e0a5719a710d8e99e5886192e937d4cd21a3cdf84a1c5bcb1cb18446eb7813ed` |
| 13_pci_dss | status==PASS | python tools/run_bench.py --only pci | {"authority": "PCI SSC", "command": "python tools/run_bench.py --only pci", "control_ids": ["1.1.1", "1.2.1", "2.2.1", "3.4.1", "3.5.1", "6.2.4", "6.4 | PASS | `10f72bdecddc0311afef12d93f4ccf50c9b7c90a9daf3401d4b474fd9d4f020b` |
| 14_iso_27001 | status==PASS | python tools/run_bench.py --only iso | {"authority": "ISO/IEC", "command": "python tools/run_bench.py --only iso", "control_ids": ["5.1.1", "5.1.2", "8.2.3", "8.24", "8.28", "8.29", "8.16", | PASS | `cd67197f8800b5f675888d1ee30a0b3035a3585032778fc0cdad8a237af21d33` |
| 15_mitre_attack | status==PASS | python tools/run_bench.py --only mitre | {"authority": "MITRE", "command": "python tools/run_bench.py --only mitre", "control_ids": ["T1078", "T1552", "T1552.001", "T1553", "T1195.002", "T122 | PASS | `b1f0d229ffa7c6a0b52915cfd14ca36bb1085ad662a313c0d6b3566730f838a8` |
| 16_ml_trl | status==PASS | python tools/run_bench.py --only 16 && cat docs/18_TRL_JUSTIFICATION/TRL.md | {"claimed_level": 8, "command": "python tools/run_bench.py --only 16 && cat docs/18_TRL_JUSTIFICATION/TRL.md", "criteria": {"changelog": {"detail": "K | PASS | `795805344db43a149dd5c03ce9f3cba9c3f274236882e348142b97177e7a7258` |

**16/16 checks passing.**

The runner's own stdout for this project is `04_Evidence/run_bench_stdout.txt`
(SHA3-256 `995f1586b16360d03096762dc19278088fab675fbb5a25526dbaac4d5b8edf56`).
