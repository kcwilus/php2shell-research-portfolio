# PHP2Shell: RCE Research and Privilege Escalation Lab

## Overview

This project documents a controlled laboratory investigation of two PHP security vulnerabilities:

- **CVE-2026-109618** — memory corruption associated with out-of-memory (OOM) handling.
- **CVE-2026-109619** — use-after-free caused by stale request-related global state.

The project focuses on understanding the vulnerability mechanisms, reproducing a public proof of concept in an isolated environment, collecting evidence of remote code execution (RCE), and investigating a separate privilege-escalation scenario.

All testing was performed against locally hosted laboratory containers.

## Project Objectives

- Understand the underlying memory-management failure modes.
- Reproduce a public RCE proof of concept in a controlled environment.
- Verify the execution context of the resulting command.
- Document a separate privilege-escalation stage inside a laboratory container.
- Preserve logs and screenshots as reproducible research evidence.
- Explain the distinction between vulnerability exploitation and privilege escalation.

## Laboratory Environment

| Component | Configuration |
|---|---|
| Host environment | Kali Linux virtual machine |
| Architecture | x86_64 |
| Container runtime | Docker |
| Orchestration | Docker Compose |
| Vulnerable target | PHP 8.4.26 with Apache `mod_php` |
| Control target | PHP 8.4.27 |
| Target application | Minimal PHP page returning `OK` |
| Network exposure | Localhost-bound laboratory ports |

The target application does not need to implement application-level session handling for the demonstrated code path to be relevant. The test concerns behavior in the PHP runtime.

## Findings

### RCE Demonstration

The public proof of concept reported successful command execution in the PHP 8.4.26 target.

Evidence recorded in `evidence/logs/rce-demo.log`:

```text
PROOF: uid=33(www-data) gid=33(www-data) groups=33(www-data)
RESULT: CONFIRMED
```

This establishes that the tested command executed with the privileges of the web-server account.

The laboratory logs also show memory-corruption messages and worker termination. These observations are relevant to the stability and reliability of the demonstrated exploit.

### Privilege-Escalation Demonstration

A separate laboratory configuration introduced an intentionally insecure `sudoers` policy. The RCE chain invoked a proof script, which ran the `id` command through that policy.

Evidence recorded in `evidence/logs/root-chain-demo.log`:

```text
PROOF: uid=0(root) gid=0(root) groups=0(root)
```

This establishes execution of the proof command as root inside the target container.

**Important:** this root result depended on the additional laboratory misconfiguration. It is not evidence that either PHP vulnerability independently grants root privileges.

## Interpretation and Limitations

- The RCE demonstration exercised a public exploit chain involving the OOM-handling and stale-state mechanisms described in the two advisories.
- The experiment did not independently establish that each CVE, in isolation, provides a complete RCE chain.
- The root demonstration depended on a deliberately insecure privilege policy added for the laboratory.
- Root inside a container is not equivalent to root on the Docker host.
- Exploit reliability may depend on the exact PHP build, memory layout, allocator behavior, worker lifecycle, and server configuration.
- The patched control behaved differently in this specific laboratory run. This result should not be interpreted as a universal security guarantee for every PHP deployment.
- The experiment is for educational research in an isolated environment and is not intended to target third-party systems.

## Evidence

| Artifact | Description |
|---|---|
| `evidence/logs/rce-demo.log` | RCE result as `www-data` |
| `evidence/logs/root-chain-demo.log` | Root proof from the combined laboratory test |
| `evidence/environment.txt` | Environment and repository metadata |
| `evidence/screenshots/` | Terminal screenshots collected during the experiment |

The screenshots retain their original filenames. Their individual contents should be reviewed before assigning descriptive filenames or captions.

## Official References

- [PHP advisory: CVE-2026-109618 — RCE via OOM corruption](https://github.com/php/php-src/security/advisories/GHSA-w8hw-w9fx-j79w)
- [PHP advisory: CVE-2026-109619 — RCE via stale global state](https://github.com/php/php-src/security/advisories/GHSA-6wmf-4rv5-p3rp)
- [Public laboratory and proof of concept used for the RCE demonstration](https://github.com/cozybear-dev/cve-2026-109618)

## Author's Notes

This repository contains research notes and evidence collected during the laboratory exercise. Third-party proof-of-concept code remains attributed to its original authors. The results describe the specific environment tested and should not be generalized beyond the demonstrated conditions.
