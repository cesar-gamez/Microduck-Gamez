# Policies

Store deployable Microduck policies here, one directory per policy:

```text
policies/
└── my-policy/
    ├── policy.onnx
    ├── manifest.json
    └── README.md
```

ONNX files are tracked with Git LFS. Install Git LFS before cloning or pushing
policy artifacts:

```bash
git lfs install
```

## Export

Always use the repository exporter. It bakes the observation normalizer into
the graph and preserves the runtime contract of 61 observations and 14 actions:

```bash
uv run scripts/export.py Mjlab-Velocity-Flat-MicroDuck \
  --wandb-run-path <entity/project/run-id> \
  --onnx-file policies/my-policy/policy.onnx
```

Do not commit PyTorch checkpoints, W&B runs, or training logs. They are large,
non-deployable intermediate artifacts and remain ignored.

## Manifest and deployment

Each deployable policy should include a schema-2 `manifest.json` following the
[Microduck policy manifest][manifest]. The supported path generates and
validates this metadata while publishing:

```bash
uv run publish \
  --onnx policies/my-policy/policy.onnx \
  --repo <hugging-face-user>/microduck-my-policy \
  --kind perpetual \
  --slot walk \
  --dry-run
```

Remove `--dry-run` when ready to publish. Use `--kind episodic --duration-s N`
for a finite skill that returns to a safe standing pose.

[manifest]: https://github.com/pollen-robotics/microduck/blob/main/docs/policy-manifest.md
