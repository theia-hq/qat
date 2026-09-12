# qat

A scale-to-zero box you `swoosh ssh` into: dormant until you dial it, one running machine while you are
in, gone when you leave. Across GitHub's NAT, by membership, with no SSH keys and no standing VM.

**The name.** *qat* is Schrödinger's box. Idle, it's in superposition: nothing running, nothing billing,
both there and not. You dial it, and the observation collapses it into one definite, running machine; close
the session and it returns to the box, unobserved. (The `q` is quantum; the rest is cat.)

## Get yours
**Prerequisite:** [`swoosh`](https://github.com/theia-hq/swoosh) on your laptop. `mint` and `ssh` are
swoosh commands (grab a binary from its [releases](https://github.com/theia-hq/swoosh/releases)).

Click **Use this template** above (or `gh repo create <you>/qat --template theia-hq/qat`), then:

```sh
swoosh mint qat                                       # authkey + records the contact me/qat
gh secret set THEIA_AUTHKEY --repo <you>/qat          # paste the authkey
gh workflow run summon.yml --repo <you>/qat -f minutes=60
# once the run is up, from your laptop:
swoosh ssh me/qat                                     # observe the cat
```

A minted badge lasts 90 days unless you pass `--expires`. Mint once per box, and for a
long-lived box mint long: `swoosh mint qat --expires 365d`. See
[`swoosh mint`](https://github.com/theia-hq/swoosh/blob/main/docs/reference/commands.md#mint).

The box serves a keyless shell plus `fetch`/`ping`/`speed`, all behind the family gate:

```sh
swoosh ping  me/qat                # round-trip time to the box
swoosh speed me/qat                # throughput to the box
swoosh fetch --via me/qat <url>    # HTTP fetched by the box, streamed back
```

## Inputs

`summon.yml` takes two inputs, passed as `-f <name>=<value>` on the `gh workflow run` line:

| input | required | default | what it is |
| ----- | -------- | ------- | ---------- |
| `minutes` | no | `30` | how long to hold the box open. |
| `services` | no | `ssh=sshd: fetch=fetch: ping=ping: speed=speed:` | the services to serve, space-separated `name=target`. |

## Teardown
The box tears itself down (and GitHub destroys the ephemeral runner, back to zero) when any of:
- the `minutes` you set elapses (default 30), or
- you end it early from your laptop: `swoosh stop --at me/qat`, or
- the job hits its hard `timeout-minutes` ceiling (6h, GitHub's per-job max).

## How it works
The runner adopts your signet and exposes a keyless shell (`sshh`) behind the family gate. Only your
devices and delegates can open a session, and your key self-signs the badge when you dial. Powered by
[`theia-hq/swoosh-action`](https://github.com/theia-hq/swoosh-action).
