## Development: Testing your changes

### Local install (simpler)

- Close Arduino IDE.
- Copy your **macchina** folder from this repository into your Arduino directory.
  - This can be either your _sketchbook_ directory (usually <Documents>/Arduino), or the directory of the Arduino application itself, the choice is up to you.
- Launch Arduino IDE and you should find the **Macchina M2** board file installed.

### Board Manager Install

- Reference the steps in see `before_deploy` in [**.travis.yml**](./.travis.yml) to create an archive from your **macchina** folder and get the size and hash.
- Grab a copy of [`package_macchina_index.json`](https://macchina.github.io/package_macchina_index.json) and edit one of the `packages.platforms` entries to match your created archive.
  - You can use either a `file://` url or [serve the files over HTTP locally](https://gist.github.com/willurd/5720255).
- Add your modified `package_macchina_index.json` under Preferences and download your board with the Board Manager.


## Upstream: ArduinoCore-sam
This variant started as a fork of the `arduino_due_x` variant in [**ArduinoCore-sam**](https://github.com/arduino/ArduinoCore-sam).  This was done in a way that preserves the ability to merge most upstream changes from the variant.

### Variant folder (`m2`)
Running the following command from a branch in **ArduinoCore-sam** prepares that branch so it can be merged with the contents in [**macchina/sam/variants/m2**](https://github.com/adamvoss/Macchina_Arduino_Boards/tree/master/macchina/sam/variants/m2) folder.  This should probably be done with some regularity, unless the variant has diverged sufficiently so merging is no longer useful.

```bash
git filter-branch --prune-empty --subdirectory-filter variants/arduino_due_x/ -f
```

### Platforms.txt / Boards.txt
Summary: it will be best to update these files manually if upstream changes happen that would be relevant/beneficial.

These files change less frequently, are shorter, and would be a greater source of merge conflicts.  To increase tidyness of the repository, they were included in such a way that it would not be easy to use git to merge changes from upstream (it remains possible, but would require `git replace`).  Since the files change infrequently, checking for changes from time to time and integrating them should be feasible.

For completeness, here is the command to prepare an **ArduinoCore-sam** branch to match the structure needed for merging with this repository.

```bash
git filter-branch \
    --force \
    --prune-empty \
    --index-filter '
        git ls-tree -z -r --name-only --full-tree $GIT_COMMIT \
        | grep -z -v "^boards.txt" \
        | grep -z -v "^platform.txt" \
        | grep -z -v "^programmers.txt" \
        | xargs -0 -r git rm -q --cached -r
    '

# Eliminate merge commits
git rebase
```