# Supplementary material: "RapidFog: Constant-Cost n-of-n Authentication of IoT Cluster Reports at the Fog"

All scripts were run with Node.js 24 (no external packages) unless stated otherwise.

## code/ : reference implementation and benchmarks (Sections 3-6 of the paper)
| File | Content |
|---|---|
| `rapidfog.js` | the protocol (nine functions), the ablation attacks, the cost, scaling and modulus-size experiments; `node rapidfog.js` |
| `bench_mod.js`, `genprime.js` | modulus-size sweep and the safe-prime generator |
| `safeprime.txt` | the 2048-bit safe prime used by all experiments |
| `results.txt/json`, `results_mod.*`, `results_hmac.txt` | raw outputs reported in the paper |

## proverif/ : mechanized model of the handshake
`rapidfog_handshake.pv` (ProVerif model), `run.sh` (how it was run), `results_proverif.txt` (output).

## iot_sim/ : constrained-node emulation and chain simulation (Section "H" of the evaluation)
| File | Content |
|---|---|
| `firmware/` | bare-metal C for a Cortex-M4 (Mbed TLS big numbers and SHA-256, ECDSA P-256 and MuSig2 baselines); `build.sh`, `run_sim.py` |
| `firmware/check_node.js` | checks the firmware outputs against an independent BigInt implementation (18/18 register values agree) |
| `network_sim.js`, `variants.js`, `baselines.js` | chain simulation over lossy IEEE 802.15.4, node-cost options, per-node baseline table |
| `results/` | instruction counts per operation (`node_ops-O2*.json`), UART logs, simulation output |

### Reproducing iot_sim
Download into `iot_sim/tools/` (not included, about 2 GB unpacked): Arm GNU toolchain `arm-none-eabi-gcc` 15.2.1 (xPack, win32-x64),
Renode 1.17.0 (windows-portable), and the Mbed TLS 3.6.8 source (`mbedtls-3.6.8`). Then
`python iot_sim/firmware/run_sim.py -O2 [bits]` (bits = 2048, 1536 or 1024) rebuilds the firmware, runs it in Renode and writes
the instruction counts; `node iot_sim/network_sim.js`, `node iot_sim/variants.js` and `node iot_sim/baselines.js` regenerate the tables.
Renode counts instructions but does not model the Cortex-M4 pipeline; times in the paper assume 1.0 and 1.5 cycles per instruction.
Renode fetches a small platform description (STM32F40x SVD) from its servers when a board is loaded.

### Notes
- Random draws of the forgery/key-recovery experiments use the operating system's entropy; exact counts vary slightly between runs.
- BLS signers were not measured; the elliptic-curve variant of the paper is a cost estimate, not an implementation.
