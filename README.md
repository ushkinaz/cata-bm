# Cataclysm Browser Module (cata-bm)

Terminal User Interface (TUI) browser for (mainly) **Cataclysm: Bright Nights** JSON data.

<a href="https://asciinema.org/a/791876" target="_blank"><img src="https://asciinema.org/a/791876.svg" /></a>


## Features
 
- Click on a property value to filter by it. Ctrl-click filters by that object's ID.
- Filter entities as you type. 
- Downloads game [data](https://data.cataclysmbn-guide.com/) directly.
- Can be used locally, if you have a copy of the game data.
- Simple search syntax:
  - `id:zombie` or `i:zombie` - Filter by ID.
  - `type:MONSTER` or `t:MONSTER` - Filter by record type.
  - `category:weapon` or `c:weapon` - Filter by category.
  - `bash.str_min:10` - Deep field search using dot-notation.
  - `term1 term2` - Combine multiple terms (AND logic).
- Switch between game versions in app.

## Installation

Go to [Releases](https://github.com/ushkinaz/cata-bm/releases) and download the latest binary for your platform.

## Controls

### Global
| Key                 | Action                               |
|---------------------|--------------------------------------|
| `Tab` / `Shift+Tab` | Cycle focus                          |
| `Ctrl+G`            | Version Switcher                     |
| `Ctrl+R`            | Reload Local Source (In-source mode) |
| `?`                 | Help Overlay                         |
| `q`                 | Quit                                 |

### Filter Input
| Key                 | Action                        |
|---------------------|-------------------------------|
| `↑` / `↓`           | Search history                |
| `Ctrl+U`            | Clear filter                  |
| `Ctrl+W`            | Delete last word              |
| `Ctrl+A` / `Ctrl+E` | Move to start / end of line   |
| `Enter`             | Confirm search and focus List |

## Usage

### Automatic Data Download
Launch the application and specify a game version. It will automatically download and cache the data for you:

```bash
cata-bm --game nightly
```

Or

```bash
cata-bm --file path/to/your/data.json
cata-bm --source path/to/cataclysm-data/
```

### Other Options
- **List available game versions**: `cata-bm --game-versions`
- **Force refresh cached data**: `cata-bm --game stable --force`

## 👴 History

When I started writing code for Cataclysm:BN [guide](https://cataclysmbn-guide.com/),
I found myself constantly trying to query the data files with simple search. Then — with jq.
The jq syntax was hard to remember, and I wrote a bunch of typical scripts to automate the process.
My final version of the jq script had complicated logic for copy-from resolving, fuzzy search, and filtering.
You can try this monstrosity yourself — just adjust the path to the JSON files and flatten.jq:

```bash
find Cataclysm-BN/data/json -name '*.json' -exec cat {} + | \
jq -s '{data: [ .[] | if type=="array" then .[] else . end ]}' | \
jq -f flatten.jq | \ 
jq -c '.[] | select(.type=="GUNMOD" and has("aim_speed"))' | \
fzf --preview 'echo {} | jq -C .'

```

[flatten.jq](flatten.jq) is a "simple" jq script that flattens game data. And I still had to change `select(.type=="GUNMOD" and has("aim_speed"))` every time.

And then I had had enough.

## 📄 License
Distributed under the MIT License. See `LICENSE` for more information.
