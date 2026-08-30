# qat

A scale-to-zero box you `swoosh ssh` into — dormant until you dial it, one running machine while you're
in, gone when you leave. Across GitHub's NAT, by membership, with no SSH keys and no standing VM.

**The name.** *qat* is Schrödinger's box. Idle, it's in superposition — nothing running, nothing billing,
both there and not. You dial it, and the observation collapses it into one definite, running machine; close
the session and it returns to the box, unobserved. (The `q` is quantum; the rest is cat.)

## Get yours
**Prerequisite:** [`swoosh`](https://github.com/theia-hq/swoosh) on your laptop — `mint` and `ssh` are
swoosh commands (grab a binary from its [releases](https://github.com/theia-hq/swoosh/releases)).

Click **Use this template** above (or `gh repo create <you>/qat --template theia-hq/qat`), then:

```sh
swoosh mint qat                                       # authkey + records the contact me/qat
gh secret set THEIA_AUTHKEY --repo <you>/qat          # paste the authkey
gh workflow run summon.yml --repo <you>/qat -f minutes=60
# once the run logs "reachable over the overlay", from your laptop:
swoosh ssh me/qat                                     # observe the cat
```

## Teardown
The box tears itself down — and GitHub destroys the ephemeral runner, back to zero — when any of:
- the `minutes` you set elapses (default 30), or
- you end early: over ssh, `touch $RUNNER_TEMP/theia-release`, or
- the job hits its hard `timeout-minutes` ceiling (6h, GitHub's per-job max).

## How it works
The runner adopts your signet and exposes a keyless shell (`sshh`) behind the family gate — so only your
devices and delegates can open a session, and your key self-signs the badge when you dial. Powered by
[`theia-hq/swoosh-action`](https://github.com/theia-hq/swoosh-action).
