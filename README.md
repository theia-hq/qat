# erwin

A template for an on-demand machine you `swoosh ssh` into — by membership, across GitHub's NAT, with no
SSH keys and no standing VM. Scale-to-zero: nothing runs (and nothing bills) until you dial it.

**The name.** *erwin* is the experiment — Erwin Schrödinger's apparatus. Adopt this template and *you* are
Erwin, the experimenter. Each box it spins up is a **qat** (**q**uantum c**at**): idle, it's in
superposition — neither running nor not; you dial it, and the observation collapses it into one definite,
running machine. Close the session and it returns to the box, unobserved.

## Get yours
Click **Use this template** above (or `gh repo create <you>/qat --template theia-hq/erwin`), then:

```sh
swoosh mint qat                                       # authkey + records the contact me/qat
gh secret set THEIA_AUTHKEY --repo <you>/qat          # paste the authkey
gh workflow run debug-ssh.yml --repo <you>/qat -f minutes=60
# once the run logs "reachable over the overlay", from your laptop:
swoosh ssh me/qat                                     # shell into your cat
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
