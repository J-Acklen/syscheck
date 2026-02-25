# Contributing to syscheck

Thanks for your interest in contributing! Here's how to help.

---

## Reporting Issues

When filing a bug, please include:
- Your distro and version (`cat /etc/os-release`)
- Your kernel version (`uname -r`)
- The full `syscheck --full` output
- Any relevant `journalctl -p err -b 0` output

## Suggesting Noise Filters

If you have a hardware-specific journal error that is confirmed harmless and
should be filtered, open an issue with:
- The exact journal line(s)
- Your hardware (CPU, GPU, motherboard/laptop model)
- Why you've confirmed it's harmless (links to upstream bug reports welcome)

## Pull Requests

1. Fork the repo and create a branch: `git checkout -b fix/my-fix`
2. Make your changes to `syscheck`
3. Run `shellcheck syscheck` and fix any warnings before submitting
4. Update `CHANGELOG.md` under a new version heading
5. Open a PR with a clear description of what changed and why

## Code Style

- Keep the script `shellcheck` clean at all times
- Add a comment explaining *why* for any non-obvious filter or threshold
- Follow the existing section structure and output formatting
- Test on a real system before submitting
