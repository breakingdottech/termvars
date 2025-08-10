# termvars

`termvars` is a lightweight command-line utility designed to manage and synchronize environment variables across multiple terminal panes or windows. It supports different scopes—current pane, new panes, or all panes—and can be used in both normal user and root environments. It provides a simple interface to set, unset, list, and reload environment variables with optional synchronization between user and root environments.

---

## Features

* Set environment variables for:

  * Current terminal pane only
  * New terminal panes
  * All terminal panes
* Unset environment variables with the same scope options
* List current environment variables or variables set for new/all panes
* Reload environment variables from stored environment files without restarting the terminal
* Synchronize environment variables between normal user and root environments with a manual sync command
* Works seamlessly with or without `sudo`, maintaining separate environment files for root and normal user contexts

---

## Use Cases

* Easily manage environment variables across multiple terminal panes in terminal multiplexers or split-window terminals like Terminator.
* Avoid repeated manual exports or reloading profiles when changing environment variables.
* Keep root and user environment variables separated and synchronized safely.
* Quickly share variables like IP addresses, domain names, usernames, or passwords across terminal panes.

---

## Requirements

* Linux or Unix-like operating system
* Bash shell (tested on Bash)
* `sudo` access if you want to manage root environment variables or install system-wide

---

## Installation

### 1. Download or clone this repository

```bash
git clone https://github.com/yourusername/termvars.git
cd termvars
```

### 2. Copy the `termvars` script to a system-wide location

You need `sudo` to write to `/usr/local/bin` because it is root-owned:

```bash
sudo cp termvars /usr/local/bin/
```

### 3. Make the script executable

```bash
sudo chmod +x /usr/local/bin/termvars
```

### 4. (Optional) For user-level installation without sudo

If you want to install for your user only (no sudo required), copy to `~/.local/bin`:

```bash
mkdir -p ~/.local/bin
cp termvars ~/.local/bin/
chmod +x ~/.local/bin/termvars
echo 'export PATH=$HOME/.local/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

---

## Configuration

### Environment files

* Normal user environment variables are stored in:

  ```
  ~/.termvars_env
  ```

* Root user environment variables are stored in:

  ```
  /etc/terminator_env_vars
  ```

### Set permissions for environment files

```bash
# For normal user environment file
touch ~/.termvars_env
chmod 600 ~/.termvars_env

# For root environment file (requires sudo)
sudo touch /etc/terminator_env_vars
sudo chmod 644 /etc/terminator_env_vars
```

### Auto-load environment variables on shell start

Add the following lines to your shell configuration file (`~/.bashrc` or `~/.zshrc`) for your user:

```bash
if [ -f ~/.termvars_env ]; then
    source ~/.termvars_env
fi
```

For root user (`/root/.bashrc`), add:

```bash
if [ -f /etc/terminator_env_vars ]; then
    source /etc/terminator_env_vars
fi
```

This ensures environment variables managed by `termvars` are loaded automatically on new terminal sessions.

---

## Usage

```
termvars [-a|-n|-i] [--sync] <command> [arguments]

Options:
  -a        Apply to all panes (default)
  -n        Apply to new panes only
  -i        Apply to current pane only
  --sync    Synchronize environment variables between root and user env files

Commands:
  set VAR VALUE      Set environment variable VAR to VALUE
  unset VAR          Unset environment variable VAR
  list               List stored environment variables (depends on scope)
  reload             Reload environment variables from environment file
```

### Examples

* Set variable for all panes:

  ```bash
  termvars set MY_VAR "my_value"
  ```

* Set variable for current pane only:

  ```bash
  termvars -i set MY_VAR "my_value"
  ```

* Unset variable in new panes only:

  ```bash
  termvars -n unset MY_VAR
  ```

* List stored variables for all panes:

  ```bash
  termvars list
  ```

* Reload environment variables from file (useful after manual edits):

  ```bash
  termvars reload
  ```

* Synchronize environment variables from root to user file:

  ```bash
  termvars --sync
  ```

* Run as root to manage root variables (requires sudo):

  ```bash
  sudo termvars set ROOT_VAR "root_value"
  ```

---

## How it works

* When run as normal user, `termvars` manages environment variables in `~/.termvars_env`.
* When run as root (or with `sudo`), it manages variables in `/etc/terminator_env_vars`.
* Variables set for "all panes" or "new panes" are stored as `export VAR="value"` lines in the respective environment files.
* Variables set for the "current pane" are exported directly in the current shell session only.
* To apply changes in all panes, terminals must source the respective environment file or be restarted. Use the `reload` command to source the environment file in the current pane without restarting.
* The `--sync` command copies variables from the root environment file to the user environment file to keep them in sync if needed.

---

## Security notes

* Environment files have restricted permissions to protect variable content.
* Normal users cannot modify root environment variables without sudo.
* Root environment variables are stored separately to maintain security and prevent accidental overwrites.
* Use the `--sync` command consciously to avoid unintended overwrites.

---

## Contributing

Contributions and improvements are welcome! Please submit pull requests or issues on GitHub.

