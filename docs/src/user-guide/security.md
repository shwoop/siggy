# Security

## Encryption model

siggy is a frontend to signal-cli, which implements the full Signal protocol.
All messages are **end-to-end encrypted in transit** -- siggy never handles
cryptographic keys or plaintext network traffic directly.

However, messages are stored **unencrypted at rest** in a local SQLite database.
This is the same approach used by Signal Desktop and most other messaging clients.
The rationale is that local storage protection is best handled at the OS level
(full-disk encryption, screen lock, file permissions) rather than by individual
applications.

### What is encrypted

- All messages between your device and Signal servers (Signal Protocol, handled
  by signal-cli)
- All media attachments in transit

### What is NOT encrypted

| File | Contents | Location |
|------|----------|----------|
| `siggy.db` | Message history, contacts, groups | Platform data directory |
| `siggy.db-wal` | Recent uncommitted writes | Same directory |
| `config.toml` | Phone number, settings | Platform config directory |
| `siggy-debug.log` | Full message content (when `--debug` enabled) | Current working directory |
| Download directory | Received attachments | `~/signal-downloads` or configured path |

## Recommendations

- **Enable full-disk encryption** on your device (BitLocker, LUKS, FileVault).
  This is the single most effective protection for data at rest.
- **Use `--incognito` mode** when you don't want any messages written to disk.
  This uses an in-memory database that is discarded on exit.
- **Avoid `--debug` in production** -- debug logs contain full message content
  and are written to the current directory with default file permissions.
- **Use a screen lock** to prevent physical access to your terminal session.

## Known limitations

- **File permissions**: siggy does not currently set restrictive file permissions
  on the database, config, or log files. They are created with your system's
  default umask. On shared systems, consider restricting permissions manually
  (`chmod 600 siggy.db config.toml`). See [#130](https://github.com/johnsideserf/siggy/issues/130).
- **Clipboard**: copied message content remains in the system clipboard until
  overwritten. See [#131](https://github.com/johnsideserf/siggy/issues/131).
- **Desktop notifications**: when enabled, notifications may show message
  previews visible on lock screens or to screen recording software. See
  [#132](https://github.com/johnsideserf/siggy/issues/132).

## Reporting vulnerabilities

If you discover a security issue, please report it responsibly via
[GitHub Issues](https://github.com/johnsideserf/siggy/issues) or contact the
maintainer directly. We take security seriously and will respond promptly.
