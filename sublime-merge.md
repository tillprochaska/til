# Sublime Merge

## Customize command palette

Sublime Merge’s command palette can be extended with custom commands. Custom commands are configured in JSON format in `~/Application Support/Sublime Merge/Packages/User/Default.sublime-commands`. For example, the following allows running `git add` with a custom pattern matching only specific files:

```json
{
	"caption": "Stage files",
	"command": "git",
	"args": {"argv": ["add", "$text"]},
}
```
