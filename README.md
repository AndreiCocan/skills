# skills

Agent skills I use with coding agents.

## Quickstart

```sh
git clone https://github.com/AndreiCocan/skills.git
cd skills
scripts/link-skills.sh
```

This symlinks every skill directory into `~/.agents/skills` and
`~/.claude/skills`, so edits in this repo take effect without re-running it.
The links point at the clone, so keep it where it is.

- `--dry-run` prints the links it would make and changes nothing.
- `AGENTS_SKILLS_DIR` and `CLAUDE_SKILLS_DIR` env vars override the two targets.

An existing path that is not a symlink is left alone and reported; the script
exits non-zero. Two skill directories with the same name abort the run.

## Skills

| Skill | Purpose |
| --- | --- |
| [aip](skills/aip/SKILL.md) | Design and implement resource-oriented APIs to Google's AIPs (aip.dev). |
| [cobra-viper](skills/cobra-viper/SKILL.md) | Go CLI architecture with Cobra and Viper: commands, flags, config, testing. |
| [code](skills/code/SKILL.md) | The smallest change that fully solves the problem: YAGNI, reuse, root cause. |
| [conventional-commits](skills/conventional-commits/SKILL.md) | Commit messages that follow Conventional Commits 1.0.0. |
| [documentation](skills/documentation/SKILL.md) | Contract-first doc comments in each language's native doc format. |
| [go](skills/go/SKILL.md) | Idiomatic Go: package design, errors, interfaces, concurrency, testing. |
| [go-release](skills/go-release/SKILL.md) | Releasing Go modules: semver, compatibility, deprecation, GoReleaser. |
| [go-spec-reviewer](skills/go-spec-reviewer/SKILL.md) | Review a Go design doc or spec before implementation starts. |
| [tdd](skills/tdd/SKILL.md) | Test-driven development: the red-green loop and tests worth keeping. |

## License

Apache License 2.0. See [LICENSE](LICENSE).
