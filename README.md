# qat

A scale-to-zero box you `swoosh ssh` into: dormant until you dispatch it, one running machine while you
are in, gone at its timer or when you stop it. Across GitHub's NAT, by membership, with no SSH keys and no
standing VM.

**The name.** *qat* is Schrödinger's box. Idle, it's in superposition: nothing running, nothing billing,
both there and not. You dispatch it, and the observation collapses it into one definite, running machine;
when the timer runs out or you stop it, it returns to the box, unobserved. (The `q` is quantum; the rest
is cat.)

This page describes the default branch.

## Get yours
**Prerequisite:** [`swoosh`](https://github.com/theia-hq/swoosh) on your laptop. `invite add` and `ssh` are
swoosh commands (grab a binary from its [releases](https://github.com/theia-hq/swoosh/releases)).

Click **Use this template** above (or `gh repo create <you>/qat --template theia-hq/qat`), then:

```sh
swoosh invite add qat                                 # one-time invite line + records the contact me/qat
gh secret set THEIA_INVITE --repo <you>/qat           # paste the invite line
gh workflow run summon.yml --repo <you>/qat -f expires=1h
# once the run is up, from your laptop:
swoosh ssh me/qat                                     # observe the cat
```

An invite is one-time and expires in 90 days unless you pass `--expires`. Each ephemeral runner adopts it
on first use; the action passes `adopt --force`, so a rerun re-adopts the same invite. See
[`swoosh invite add`](https://github.com/theia-hq/swoosh/blob/main/docs/reference/commands/invite.md).

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
| `expires` | no | `30m` | how long to hold the box open (a duration, e.g. `30m`, `2h`). |
| `services` | no | `ssh=sshd: fetch=fetch: ping=ping: speed=speed:` | the services to serve, space-separated `name=target`. |

## Teardown
The box tears itself down (and GitHub destroys the ephemeral runner, back to zero) when any of:
- the `expires` you set elapses (default `30m`), or
- you end it early from your laptop: `swoosh stop --at me/qat`, or
- the job hits its hard `timeout-minutes` ceiling (6h, GitHub's per-job max).

## How it works
The runner adopts your signet and exposes a keyless shell (`sshh`) behind the family gate. Only your
devices and delegates can open a session, and your key self-signs the badge when you dial. Powered by
[`theia-hq/swoosh-action`](https://github.com/theia-hq/swoosh-action).
