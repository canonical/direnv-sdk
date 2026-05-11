# direnv SDK for Workshop

This SDK provides [direnv](https://direnv.net/) inside a workshop. It installs
the upstream binary and wires the bash, zsh, and fish shell hooks system-wide.
On project setup it auto-allows `/project/.envrc`, so the project's environment
loads on every new shell. The direnv configuration directory is persisted
between workshop updates, so its allowlist survives refreshes.

---

## Reference workshop

A minimal workshop:

```yaml
# workshop.yaml
name: my-project
base: ubuntu@24.04
sdks:
  - name: direnv
    channel: latest/stable
```

The workshop user gets direnv on the PATH and an active hook in every
interactive bash, zsh, or fish shell. Place an `.envrc` in your project
directory and its exports load automatically on `workshop shell`.

---

## Using the SDK

### Prerequisites, project layout

1. No prerequisite SDKs are required.
2. Put your project files in your project directory (mounted at `/project/`
   inside the workshop). To use the SDK, add an `.envrc` file at
   `/project/.envrc` with the exports you want active in every shell:

   ```bash
   # /project/.envrc
   export DATABASE_URL=postgres://localhost/dev
   export NODE_ENV=development
   PATH_add ./bin
   ```

3. On launch, the SDK adds direnv to `PATH`, installs the bash/zsh/fish
   shell hooks under `/etc/`, and — if `/project/.envrc` exists — calls
   `direnv allow /project` so the hook is trusted without manual approval.

### Start a session

Once the workshop is ready:

```bash
workshop shell
```

The hook fires on shell start; your `.envrc` exports are active immediately.
direnv prints a one-line summary of the variables it loaded on every
directory change. Edit the `.envrc` and direnv re-reads it on the next prompt.

If you edit `.envrc` and direnv complains the file is no longer allowed (it
hashes the file on `allow` and re-prompts on changes), run:

```bash
direnv allow
```

### Use direnv to manage secrets for other SDKs

direnv is a convenient place to set environment variables consumed by other
workshop SDKs — for example API tokens for agentic SDKs like
[claude-code](https://github.com/canonical/claude-code-sdk),
[codex](https://github.com/canonical/codex-sdk),
[copilot](https://github.com/canonical/copilot-sdk), or
[opencode](https://github.com/canonical/opencode-sdk). Put the relevant
variable in `/project/.envrc` and it will be in scope wherever those SDKs
look for it:

```bash
# /project/.envrc
export ANTHROPIC_API_KEY=...
export OPENAI_API_KEY=...
```

---

## Plugs (resources this SDK consumes)

### `direnv-config`

- Interface: `mount`
- Workshop target: `/home/workshop/.config/direnv`
- Purpose: Preserves direnv's user configuration between workshop updates.
  This is where direnv looks for `direnvrc` (extensions to the direnv
  stdlib) and `direnv.toml`.

### `direnv-state`

- Interface: `mount`
- Workshop target: `/home/workshop/.local/share/direnv`
- Purpose: Preserves direnv's allowlist between workshop updates. direnv
  stores the per-`.envrc` approval hashes under `allow/` here; without this
  mount, every refresh would force the user to re-approve their `.envrc`
  files.

## Slots (resources this SDK provides)

This SDK doesn't define any slots.

---

## Documentation and guidance

- [direnv official documentation](https://direnv.net/)
- [direnv stdlib reference](https://direnv.net/man/direnv-stdlib.1.html)
- [Workshop documentation](https://canonical-workshop.readthedocs-hosted.com/latest/)

---

## Community and support

- direnv project: [direnv on GitHub](https://github.com/direnv/direnv)
- Workshop forum:
  [Workshop Discourse](https://discourse.canonical.com/c/engineering/workshops/34)
- Please review our
  [Code of Conduct](https://ubuntu.com/community/ethos/code-of-conduct)
  before participating.

---

## Contributions

All contributions, including code, documentation updates, and issue reports,
are welcome!

- See `CONTRIBUTING.md` for guidelines.
- Open issues or pull requests on the
  [official repository](https://github.com/canonical/direnv-sdk).

---

## License and copyright

Copyright 2026 Canonical Ltd.

This SDK packaging is licensed under the
[MIT License](https://opensource.org/licenses/MIT).

direnv itself is licensed under the
[MIT License](https://github.com/direnv/direnv/blob/master/LICENSE).
