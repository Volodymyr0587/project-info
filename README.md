# project-info

A lightweight Bash CLI utility that scans a directory and displays an overview of discovered projects, including their last modification date, size, project type, Git branch, and working tree status.

## Features

- **Automatic project discovery** — detects projects using Git repositories, project manifests, entry points, and explicit markers.
- **Project type detection** — recognizes Laravel, Vue, React, PHP, Python, Rust, Go, JavaScript, HTML/CSS, Bash, Make, and generic Git projects.
- **Bash detection** — recognizes `.sh` and `.bash` files, as well as files with a Bash shebang.
- **Project filtering** — display only projects matching one or more types using `--type`.
- **Git integration** — displays the current branch and working tree status.
- **Git-only mode** — show only Git repositories.
- **Sorting** — sort projects by modification date, name, or size.
- **Depth control** — limit how deeply the utility searches for projects.
- **Result limit** — display only a specified number of projects.
- **Verbose output** — include project category and full directory path.
- **Dependency directory exclusion** — skips common dependency, cache, and build directories.
- **Manual project marker** — use `.project-info` to explicitly identify a directory as a project.

## Requirements

- Linux
- Bash 4.0+
- GNU `find`, `du`, `stat`, `sort`, `date`, and `numfmt`
- Git (optional, for Git information)

## Installation

Make the script executable:

```bash
chmod +x project-info
```

Optionally, install it system-wide:

```bash
sudo install -m 755 project-info /usr/local/bin/project-info
```

You can then run it from any directory:

```bash
project-info
```

## Usage

```text
project-info [OPTIONS]
```

### Options

| Option             | Description                                                |
| ------------------ | ---------------------------------------------------------- |
| `-r, --root DIR`   | Root directory to scan                                     |
| `-d, --depth N`    | Maximum directory depth                                    |
| `-n, --limit N`    | Maximum number of projects to display; `0` means unlimited |
| `-s, --sort FIELD` | Sort by `date`, `name`, or `size`                          |
| `-t, --type TYPE`  | Filter projects by type; accepts comma-separated values    |
| `-g, --git`        | Show only Git repositories                                 |
| `-v, --verbose`    | Display category and full project path                     |
| `-h, --help`       | Display help                                               |
| `--version`        | Display version                                            |

### Examples

Display projects in the default directory:

```bash
project-info
```

Scan a specific directory:

```bash
project-info --root ~/Projects
```

Search up to four directory levels:

```bash
project-info --depth 4
```

Display the ten most recently modified projects:

```bash
project-info --sort date --limit 10
```

Sort projects by size:

```bash
project-info --sort size
```

Sort projects alphabetically:

```bash
project-info --sort name
```

Display only Bash projects:

```bash
project-info --type Bash
```

Display multiple project types:

```bash
project-info --type "Laravel,Vue,React"
```

Display HTML/CSS and JavaScript projects:

```bash
project-info --type "HTML/CSS,JavaScript"
```

Display only Git repositories:

```bash
project-info --git
```

Combine Git filtering with project type filtering:

```bash
project-info --git --type Bash
```

Display detailed information, including full paths:

```bash
project-info --verbose
```

Combine multiple options:

```bash
project-info --root ~/Desktop/projects \
             --depth 4 \
             --sort date \
             --limit 20 \
             --verbose
```

## Project Discovery

The utility identifies project directories using several methods.

### Automatic markers

A directory is recognized as a project when it contains one or more of the following:

- `.git` — Git repository or worktree
- `composer.json` — Composer/PHP project
- `package.json` — JavaScript project
- `pyproject.toml` — Python project
- `setup.py` — Python project
- `requirements.txt` — Python project
- `Cargo.toml` — Rust project
- `go.mod` — Go project
- `artisan` — Laravel application
- `Makefile` — Make-based project

### Bash projects

Bash projects are recognized by:

- `.sh` files
- `.bash` files
- Files with a Bash shebang

A standalone Bash project without Git should have root-level shell scripts and a `README.md`.

Generic internal directories such as `tests`, `test`, `src`, `docs`, `bin`, and `scripts` are not automatically identified as standalone Bash projects.

### Explicit project marker

You can manually mark any directory as a project by creating an empty `.project-info` file:

```bash
touch .project-info
```

This is useful for directories that do not contain standard project manifests.

## Project Types

The utility recognizes the following types:

| Type        | Detection                                            |
| ----------- | ---------------------------------------------------- |
| Laravel     | `artisan`                                            |
| Vue         | `package.json` with Vue dependency                   |
| React       | `package.json` with React dependency                 |
| PHP         | `composer.json`                                      |
| Python      | `pyproject.toml`, `setup.py`, or `requirements.txt`  |
| Rust        | `Cargo.toml`                                         |
| Go          | `go.mod`                                             |
| JavaScript  | `package.json` or static HTML/JavaScript structure   |
| HTML/CSS    | `index.html` and CSS files                           |
| Bash        | Shell scripts or Bash shebang                        |
| Make        | `Makefile`                                           |
| Git project | Git repository without a more specific detected type |
| Unknown     | Explicitly marked project without a recognized type  |

More specific project types take precedence over generic types.

## Git Information

For Git repositories, the output includes:

- **Branch** — current Git branch
- **Status** — working tree status

Possible status values:

| Status  | Meaning                                             |
| ------- | --------------------------------------------------- |
| `Clean` | No uncommitted changes or untracked files           |
| `Dirty` | Uncommitted changes or untracked files              |
| `-`     | Not a Git repository or Git information unavailable |

The utility does not report the status of a parent repository for a nested directory.

## Excluded Directories

To avoid scanning dependencies, caches, and generated files, the utility skips common directories:

- `.git`
- `node_modules`
- `vendor`
- `.idea`
- `.vscode`
- `__pycache__`
- `.pytest_cache`
- `.mypy_cache`
- `.venv`
- `venv`
- `dist`
- `build`
- `coverage`
- `.next`
- `.nuxt`
- `target`
- `.cache`

These exclusions apply to project discovery and file scanning. Project size calculation also excludes the main dependency and build directories.

## Output

The standard output contains:

| Column        | Description                               |
| ------------- | ----------------------------------------- |
| PROJECT       | Project directory name                    |
| LAST MODIFIED | Last modification date of project files   |
| SIZE          | Project size                              |
| TYPE          | Detected project type                     |
| BRANCH        | Current Git branch                        |
| STATUS        | Git working tree status                   |
| CATEGORY      | First-level directory under the scan root |

With `--verbose`, the output also includes the full project path.

## Version

**Current version: 1.2.0**

### Changelog

#### 1.2.0

- Added `--type` / `-t` option for filtering by project type.
- Added support for comma-separated type filters.
- Improved Bash project detection.
- Added `.bash` file and Bash shebang recognition.
- Added HTML/CSS project detection.
- Added `.project-info` as an explicit project marker.
- Reduced false-positive detection of internal directories such as `tests`.
- Improved project discovery and type classification.

#### 1.1.0

- Improved project type detection.
- Added excluded directories for dependencies and build artifacts.
- Improved Git repository handling.
- Added project size and verbose output.

#### 1.0.0

- Initial release.
- Project discovery.
- Git branch and status detection.
- Sorting and result limit options.
