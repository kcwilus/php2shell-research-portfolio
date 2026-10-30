# Screenshot Evidence

Screenshots collected during testing in an isolated local Docker laboratory.

| Screenshot | Description |
|---|---|
| `01-lab-containers-and-http-baseline.png` | Laboratory container status and baseline HTTP response |
| `02-vulnerable-vs-patched-php-versions.png` | Comparison of PHP 8.4.26 and PHP 8.4.27 control |
| `03-rce-proof-as-www-data-patched-control.png` | RCE proof as `www-data` and observed control-test result |
| `04-intentionally-insecure-sudoers-lab-config.png` | Deliberately insecure sudoers configuration used for the lab |
| `05-root-proof-inside-target-container.png` | Proof of command execution as root inside the target container |

## Important Limitations

The root-stage result depends on an additional, intentionally insecure sudoers policy. It does not demonstrate that PHP2Shell itself grants root privileges, nor that the Docker host was compromised.

The screenshots supplement the raw logs in `../logs/`. Results describe the specific laboratory configuration tested.
