# VS Code — installed properly

<!-- audion:release -->
[![Windows](https://img.shields.io/badge/Windows-10%20%7C%2011-0b6db8?style=flat-square&logo=windows&logoColor=white)](https://audion.dev/downloads/setup-for-vs-code) [![Release](https://img.shields.io/github/v/release/Tensionix/setup-for-vs-code?style=flat-square&label=release&color=e08a63)](https://github.com/Tensionix/setup-for-vs-code/releases/latest) [![Downloads](https://img.shields.io/github/downloads/Tensionix/setup-for-vs-code/total?style=flat-square&label=downloads&color=5fd08a)](https://github.com/Tensionix/setup-for-vs-code/releases) [![License](https://img.shields.io/github/license/Tensionix/setup-for-vs-code?style=flat-square&color=5fd08a&logo=apache&logoColor=white&cacheSeconds=3600)](https://github.com/Tensionix/setup-for-vs-code/blob/main/LICENSE)

**Version 1.0.0** · 2026-08-25 · 792 KB

- [Direct download](https://audion.dev/get/setup-for-vs-code/1.0.0/Audion_Setup_for_VS_Code_v1.0.0_Full.zip) — unmetered, no rate limits
- [Project page](https://audion.dev/downloads/setup-for-vs-code) — every version and how to install

`SHA-256: f097ea53ee055d0d07ee8dd1eb37bcddaeecd1f648e9531e4632e47e391a8abb`

---

An **Audion** tool, published by [Tensionix](https://github.com/Tensionix).
<!-- /audion:release -->

A portable kit: fetch the current build, install it with every wizard option and
configure it straight away — no folder-trust prompts, with the extensions you
want. Works both with an ordinary installation and with a portable copy.

| Command | What it does |
| --- | --- |
| `Download-Installer.cmd` | fetch the machine-wide installer |
| `Download-Installer.cmd -Kind user` | the per-user installer instead |
| `Download-Portable.cmd` | fetch the archive for portable use |
| `Download-Installer.cmd -Check` | only report the current version |
| `Install-VSCode.cmd` | install, configure, core extensions, Claude and Codex |
| `Install-VSCode-Claude.cmd` | the same, but Claude as the only agent |
| `Install-VSCode-AllAI.cmd` | the same, with all four agents |
| `Install-VSCode-NoAI.cmd` | the same, with no agents at all |
| `Install-VSCode.cmd -Check` | show what is installed and what is enabled |
| `Install-VSCode.cmd -SettingsOnly` | settings and extensions only |
| `Setup-Portable.cmd` | portable: core, Claude and Codex (≈3.1 GB) |
| `Setup-Portable-Claude.cmd` | portable: core and Claude (≈2.3 GB) |
| `Setup-Portable-AllAI.cmd` | portable: core and all four agents (≈3.5 GB) |
| `Setup-Portable-NoAI.cmd` | portable: core only (≈2.0 GB) |
| `Update-Portable.cmd` | update the portable copy to the current version |
| `Start-Portable-VSCode.cmd` | launch the portable copy (created during setup) |

**Updating.** An installed VS Code updates itself through Microsoft's own
mechanism and needs no command from us. A portable copy does not update itself —
that is the developers' decision: an update would replace the whole folder
together with `data\`. So `Update-Portable.cmd` does it: fetches the current
archive, replaces the program and leaves `data\` with its settings and
extensions alone.

Four variants of each kind exist because of what the agents weigh: Codex takes
826 MB, Claude Code 283 MB, Gemini 237 MB. Each carries its own runtime with the
engine inside, hence the size. Grok is the exception at 11 MB — it is only a
panel, and its engine lives outside, in a separately installed CLI.

## How this differs from an ordinary install

The VS Code wizard is Inno Setup, and its checkboxes are listed for silent mode
in `/MERGETASKS`. The script enables every one of them except launching the
editor afterwards:

| Task | What it gives |
| --- | --- |
| `addcontextmenufiles` | "Open with Code" for files |
| `addcontextmenufolders` | the same for folders |
| `associatewithfiles` | Code becomes the editor for its file types |
| `addtopath` | `code` works from the console |
| `desktopicon` | a desktop shortcut (disabled with `-NoDesktopIcon`) |
| `!runcode` | do not start the editor right away |

## The main thing: no trust prompts

Workspace trust is switched off entirely in `settings.json`:

```json
"security.workspace.trust.enabled": false
```

This is the key the previous settings were missing. Without it the editor keeps
asking about every folder, no matter how much you edit `banner`,
`startupPrompt` and `untrustedFiles` — those soften the question, they do not
remove it.

Settings are written with a backup of the previous ones: a
`settings.json.<date>.bak` appears beside them.

## Self-updating

**An installed VS Code** updates itself — `update.mode: default` and background
updates are on in the settings, and extensions update themselves too.

**A portable copy does not update itself**, and that is the developers'
decision: an update would replace the whole folder along with `data`, where the
settings and extensions live. So `Update-Portable.cmd` does it: fetches the
current archive, replaces the program and does not touch `data`. The extensions
inside still update themselves.

## Portable mode

Portability is switched on by a single folder: if a `data` directory sits beside
`Code.exe`, the editor keeps everything of its own there and writes nothing into
the user profile. So the settings go to `data\user-data\User\settings.json` and
the extensions to `data\extensions`. The folder can be carried away whole.

To start it, `Setup-Portable` puts `Start-Portable-VSCode.cmd` in the root — a
double click raises the editor, with no diving into `VSCode\` for `Code.exe`.
The path inside is relative (`%~dp0`), so the shortcut travels with the folder;
deploy elsewhere with `-TargetDir` and the shortcut gets the full path instead.

### What portability does not carry

Portable mode isolates the **editor**: settings, extensions and window state live
in `data`. It cannot isolate what an extension keeps outside, in the user
profile — and that shows immediately.

Claude Code turns out to be **already signed in** inside the portable copy, even
though the sign-in happened in the installed one. The reason: the token is not
in VS Code but in `%USERPROFILE%\.claude\.credentials.json` — the shared Claude
Code profile, one for the CLI and for the extension. The portable editor simply
reads it from there.

Others behave the same way: Git credentials go to the Windows credential
manager, the Docker context to `~/.docker`, the Grok Build sign-in to
`%USERPROFILE%\.grok`. All of it comes from the system, not from the editor's
folder.

The flip side of the same rule: on **someone else's** machine none of it will be
there — `data` carries no tokens. Take the flash drive away and the
authorization stays at home. From a security standpoint that is exactly right.

## Extensions

There are three lists, read the same way — a line can simply be moved from one
to another:

| File | Who installs it |
| --- | --- |
| `extensions.txt` | every deployer (21 extensions) |
| `extensions-ai.txt` | the ordinary one, `-Claude` and `-AllAI`; parsed by section |
| `extensions-optional.txt` | only with the `-WithOptional` switch |

### Four agents, and what each of them costs

`extensions-ai.txt` has four sections, and the deployers read them by heading:
the ordinary one takes Claude and Codex, `-Claude` takes one, `-AllAI` takes all,
`-NoAI` takes none. Each has its own story.

**Claude Code** and **Codex** work by subscription and require nothing around
them. That is the canon, and they are what the ordinary deployer installs.

**Gemini Code Assist** is included, with a caveat worth knowing in advance:
**since 18 June 2026 the extension no longer serves personal plans** — neither
"Code Assist for individuals", nor Google AI Pro, nor Ultra. Google moved
personal accounts to Antigravity, its own IDE with its own CLI; there is no
plugin for someone else's VS Code any more. The extension is alive and updated,
but it answers only to a Code Assist Standard or Enterprise licence — that is,
through an organization and a Google Cloud project. A 1.9 rating at five million
installs is exactly about this. On a personal account it installs and stays
silent.

**Grok** has no extension from xAI at all. What ships here is Paweł Huryn's
community wrapper: a panel that talks to the **Grok Build CLI** over JSON-RPC
through `grok agent stdio`. So the CLI itself is needed:

```
irm https://x.ai/cli/install.ps1 | iex
grok login
```

The deployer deliberately does not install the CLI — that is running a script
from the network, and such a thing belongs in a deliberate command, not inside
an installer. What it does do is check whether `grok` is in `PATH` or in
`%USERPROFILE%\.grok\bin`, and say plainly that the panel will stay empty if it
is not.

### Signing in to Grok Build by subscription

Signing in by subscription is the default; nothing has to be switched on. Just
start the CLI:

```
grok
```

The first run opens the browser itself and walks through xAI OAuth at
`auth.x.ai`. A **SuperGrok** or **X Premium+** subscription will do; a key from
the xAI console is not needed for this. To sign in again, to change accounts or
to repair a broken session:

```
grok login
```

The command has an `--oauth` flag, but it is implied anyway. To sign out, `grok
logout` — it also clears the stored data.

**Where the sign-in is kept.** In `%USERPROFILE%\.grok\auth.json`. Tokens
refresh themselves in the background; if the server named no lifetime, the data
lives for 30 days. The file is shared by everything that uses the CLI — which is
why the Grok panel in VS Code asks for no separate sign-in, and why it turns out
authorized in a portable copy too. For the same reason it will not be there on
someone else's machine: the editor's `data` carries no tokens.

**Do not carry `auth.json` by hand.** Technically it works: the CLI watches the
file and picks up replaced data on the fly, without a restart. But this is the
account key in the clear — a copy on a flash drive equals a copy of the password
without a second factor. The data also expires after 30 days, and xAI's token
rotation, when the account is used from two places, leads to a repeated
sign-in: carrying the file risks not adding access on the new machine but taking
it away on the old one. For a second machine there is the way below, and it
takes half a minute.

**Without a browser** — on a server over SSH, in a container, on a remote
machine:

```
grok login --device-auth
```

The command prints a link and a code: open the link on any device, enter the
code, and the CLI waits for the confirmation itself.

**An API key is not the same thing, and it does not replace a subscription.**
The order is: first a key bound to a specific model in `config.toml`, then the
active session from `auth.json`, and only then `XAI_API_KEY`. So keeping a key
"just in case" is harmless — while the session lives, the usage goes against the
subscription. The reverse is true as well: to make the work go through the key
and the console's billing, the session has to be closed first — `grok logout`.
In PowerShell the key is set like this:

```
$env:XAI_API_KEY = "xai-..."
```

**What it consumes.** Calls, including search across X, come not from separate
credits but from the **shared weekly pool** of the subscription — the same one
the chat at grok.com runs on. So frequent searching through the feed is not
free: it is paid for not in money but in a week. xAI does not publish the price
of a single request; the remainder is shown by `/usage` inside the TUI (where
`/login` and `/logout` live too) and under Settings → Usage on grok.com.

The full description is in xAI's own documentation, which arrives with the CLI:
`%USERPROFILE%\.grok\docs\user-guide\02-authentication.md`. It also covers what
we do not need but a workplace might: signing in through a corporate IdP over
OIDC (Okta, Azure AD, Auth0) and handing authentication to an external script.

One identifier per line; lines starting with `#` are skipped. They are installed
with `--force`: it removes the publisher-trust question, which in a silent
install there is nobody to answer anyway.

A readable list lives in `Extensions.html`: the "Install" button opens the
extension in the editor through the `vscode:extension/…` protocol, the second
one copies the command to the clipboard. The page is built from the same files
by `Build-ExtensionsPage.cmd`, so it cannot drift apart from them.

### Todo Tree and ripgrep

The extension looks for markers through ripgrep and complains on first run:
"Failed to find vscode-ripgrep… set `todo-tree.ripgrep`". The advice is wrong:
the key is actually **`todo-tree.ripgrep.ripgrep`** — the extension reads the
`todo-tree.ripgrep` section and takes the `ripgrep` property out of it.

VS Code carries its own `rg.exe`, but the path to it contains a build hash and
changes with every update, so it cannot be written down. The scripts install
ripgrep separately (`winget install BurntSushi.ripgrep.MSVC`) and substitute the
path during installation — it is not stored ready-made in the file, or the kit
could not be moved to another machine.

Microsoft and GitHub as publishers are trusted by the editor itself; the rest
would ask for confirmation on first install.

## Where the builds come from

Microsoft keeps permanent addresses that always lead to the current release, so
no version is written down by hand. The version and the checksum are reported by
the update service
(`update.code.visualstudio.com/api/update/<platform>/stable/latest`), and the
file is verified by sha256. If the service is unreachable, the direct link
remains — the file name comes from the redirect, only without the checksum
verification.

What is downloaded lands in the `Download\` subfolder; the installer and the
portable setup look for it there (and will still find it beside the script, the
old way). The previous build of the same kind is removed: the newest is what
gets used, and three versions only take up space.
