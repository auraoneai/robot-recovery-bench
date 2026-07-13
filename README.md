# robot-recovery-bench

`robot-recovery-bench` validates JSONL records for human intervention and robot
recovery segments, then summarizes outcome and timing metrics without loading
robot media.

## At a Glance

| | |
| --- | --- |
| Job | Make recorded intervention and recovery segments structurally reviewable and comparable. |
| Built for | Robotics data curators, HIL researchers, teleoperation teams, and training-data reviewers. |
| Differentiator | Dependency-free schema checks and aggregate metrics over metadata-only segment records. |
| Produces | Validation errors or Markdown/JSON recovery metrics and failure-reason clusters. |

## Install

```bash
python -m pip install "robot-recovery-bench==0.1.2"
```

## Verified Quickstart

Run from a source checkout:

```bash
robot-recovery-bench validate examples/mock_recovery_segments.jsonl

robot-recovery-bench report examples/mock_recovery_segments.jsonl \
  --format json \
  --out /tmp/robot-recovery-report.json
```

The bundled fixture validates three segments and reports a recovery success rate
of `0.6667` and a training-ready rate of `0.6667`.

## Record and Metric Contract

Each segment records an episode ID, task, failure reason, intervention type,
start/intervention/end timestamps, and recovery result. Optional fields include
operator action and whether the segment is marked training-ready.

Reports include:

- segment count;
- recovery success and training-ready rates;
- average time to intervention and recovery duration;
- failure-reason cluster counts.

The JSON metric `intervention_rate` is `1.0` whenever segments are present
because every input row is already an intervention segment. It is not an
episode-level, dataset-level, or fleet-level intervention prevalence estimate.

Python adapters can normalize LeRobot intervention lists and RLDS steps into the
segment shape; the CLI itself accepts JSONL.

## Runtime, Data, and Network Boundary

- Validation and reporting read local JSONL and write a local aggregate report.
- The package does not load video, connect to a robot, execute a policy, or make
  network requests.
- No redaction is applied. Aggregate reports can preserve failure-reason labels,
  and validation output identifies line numbers, so review labels before sharing
  results.
- Metrics describe only the supplied segments and do not infer unrecorded
  failures, operator quality, causal safety, or real-world recovery capability.

## Limitations

- The CLI accepts normalized JSONL segment records only. Upstream conversion
  from RLDS, LeRobot, or in-house formats remains the caller's job.
- Aggregate rates are segment-level summaries, not episode-level or fleet-level
  reliability claims.

## Compatibility

`robostudio-engine` exposes a direct `robostudio recovery` integration that
imports this package when installed or available in the AuraOne monorepo.

## Publication Status

Verified on 2026-07-13:

- PyPI: [`robot-recovery-bench==0.1.2`](https://pypi.org/project/robot-recovery-bench/0.1.2/)
- GitHub release: [`v0.1.2`](https://github.com/auraoneai/robot-recovery-bench/releases/tag/v0.1.2)
- Bundled segment records are synthetic tutorial data, not benchmark evidence.

## Next Action

Normalize one reviewed batch of intervention segments, validate it, and inspect
the failure-reason clusters before deciding whether any records are
training-ready.
