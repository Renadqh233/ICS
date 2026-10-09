# PLC Timing Baseline

A simple threshold baseline for identifying unusual PLC snapshot rows using shared timing fields from PLC1 and PLC2.

## Scope

This analysis covers Normal, Fault, and Attack snapshots for PLC1 and PLC2 from the current data collection. PLC1 and PLC2 are reported separately as well as together.

## Method

- Use the first 70% of each PLC's Normal data for training.
- Combine both training portions to calculate shared 99th-percentile thresholds.
- Evaluate on the remaining 30% of Normal data and all available Fault and Attack rows.
- Flag a row if any of the four timing conditions exceeds its threshold.

## Combined thresholds

| Feature | Threshold |
| --- | ---: |
| `loop_latency` | 8 |
| `logic_execution_time` | 29 |
| Time between rows | 0.22788638 seconds |
| `current_loop` step | 200 |

## Results by PLC

| PLC | Evaluation group | Flagged rows | Total rows | Flagged rate |
| --- | --- | ---: | ---: | ---: |
| PLC1 | Normal test | 534 | 24,487 | 2.18% |
| PLC1 | Fault | 8,749 | 14,620 | 59.84% |
| PLC1 | Attack | 767 | 3,501 | 21.91% |
| PLC2 | Normal test | 593 | 24,486 | 2.42% |
| PLC2 | Fault | 7,425 | 14,627 | 50.76% |
| PLC2 | Attack | 1,598 | 4,840 | 33.02% |

## Combined results

| Group | Flagged rows | Total rows | Flagged rate |
| --- | ---: | ---: | ---: |
| Normal test | 1,127 | 48,973 | 2.30% |
| Fault | 16,174 | 29,247 | 55.30% |
| Attack | 2,365 | 8,341 | 28.35% |

## Interpretation

The baseline flags a small portion of held-out Normal rows and a larger portion of Fault and Attack rows. The results differ between PLC1 and PLC2, especially for Attack. Report both the combined results and the per-PLC results.

Flagged rates are row-level rates, not final model accuracy or event-level detection rates. A row is counted once even if it exceeds multiple thresholds.

## Data notes

The PLCs have different process-variable schemas. PLC1 contains tank-related fields, while PLC2 contains conveyor and bottle-related fields. The shared baseline uses timing fields and `current_loop` only.

The notebook expects the snapshot files to be available in the Colab `/content` directory:

- `normal_snapshots_PLC1.xlsx`
- `faults_snapshots_PLC1.xlsx`
- `attack_snapshots_PLC1.xlsx`
- `normal_snapshots_PLC2.xlsx`
- `faults_snapshots_PLC2.xlsx`
- `attack_snapshots_PLC2.xlsx`

## Limitations and next steps

- This is an exploratory rule-based baseline, not a machine-learning model.
- Validate the meaning and units of the PLC fields before using the thresholds operationally.
- Apply the same methodology to additional independent datasets before describing the work as a multi-dataset benchmark.
- Review timestamp alignment before combining PLC snapshots with syslog or PCAP data.
