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

## Store SSH key password

Sublime Key uses the default SSH key in `~/.ssh/` for authentication agains remote repositories. Usually it will prompt for the password if the key is protected, but if it doesn’t or in order to persist the password, the following command can be used on macOS:

```
ssh-add --apple-use-keychain KEY_PATH
```
