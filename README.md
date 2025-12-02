# FocusPocus

FocusPocus is a tiny Bash spell that toggles distractions on or off by rewriting `/etc/hosts`. It flips between **focus** (only work) and **pocus** (temporary play) so you can keep social sites out of reach until you truly earn the break. The project is authored by [Mike Lopez](https://mikelopez.com).

## Features
- Works entirely through `/etc/hosts`, so it is desktop-environment agnostic.
- Uses `#distraction` markers to decide which hostnames should be blocked or unblocked.
- `focus` (default) instantly re-enables blocking and prints a success banner when the command completes.
- `pocus` requires solving two random multiplication challenges before unblocking, helping you pause before drifting off-task.
- All logic lives in a single portable Bash script; no daemons or background services.

## Requirements
- Bash 4+, Perl (the script now checks for it and exits early if missing), and `sudo` access (FocusPocus calls `sudo perl -pi` to edit `/etc/hosts`).
- `/etc/hosts` entries annotated with `#distraction` for every site you want FocusPocus to manage.
- macOS, Linux, or any Unix-like OS where editing `/etc/hosts` is effective.

## Installation
1. Copy the `focuspocus` script somewhere in your `PATH`, e.g. `/usr/local/bin/focuspocus`.
2. Mark it executable: `chmod +x /usr/local/bin/focuspocus`.
   - Fast install one-liner (downloads the script directly from `main`):
     ```bash
     (sudo curl -fsSL https://raw.githubusercontent.com/easterncoder/focuspocus/main/focuspocus -o /usr/local/bin/focuspocus || sudo wget -qO /usr/local/bin/focuspocus https://raw.githubusercontent.com/easterncoder/focuspocus/main/focuspocus) && sudo chmod +x /usr/local/bin/focuspocus
     ```
3. Add distraction entries to `/etc/hosts`, for example:
   ```
   127.0.0.1 facebook.com www.facebook.com #distraction
   127.0.0.1 twitter.com www.twitter.com #distraction
   127.0.0.1 youtube.com www.youtube.com #distraction
   ```
4. (Optional) Create a backup copy of `/etc/hosts` in case you need to restore it later.

## Usage
```bash
# Block distracting sites (default)
focuspocus

# Explicit focus mode block
focuspocus focus

# Attempt to unblock (requires two correct math answers)
focuspocus pocus
```

### What happens in each mode?
- `focus`: Runs `sudo perl -pi -e 's/^[#\s]+(.+?#distraction)/\1/' /etc/hosts` to uncomment every distraction line and re-enable blocking, then prints a confirmation banner. If the edit fails, you will see an error banner instead.
- `pocus`: Prints a warning, then asks two multiplication questions (one single digit × one double digit). Only if both are correct does it comment out each distraction line (`#` prefix) and allow access. Any incorrect answer or editing error leaves the block in place and produces a failure banner.

## Customizing distractions
- Add or remove hostnames by editing `/etc/hosts` and keeping `#distraction` on every line you want the script to control.
- Lines without `#distraction` are untouched, so you can safely keep other overrides or local domains in the file.
- Use IPv6 (`::1`) entries as well if your browser prefers IPv6; just append `#distraction` so FocusPocus can find them.

## Safety notes
- FocusPocus modifies `/etc/hosts` directly. Always verify the file after big changes or keep it under version control.
- Commands run with `sudo`, so you will be prompted for your password; cancel with `Ctrl+C` if something looks wrong.
- If the script appears ineffective, flush your DNS cache or toggle network interfaces so the OS loads the new `hosts` entries.

## License
FocusPocus is distributed under the GNU General Public License v3.0 or later. See the script header for the full notice or visit <https://www.gnu.org/licenses/>.
