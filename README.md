frolicd
========

frolic through directories by typing small part of their names.

__frolicd__ is a keyboard-based directory browser. its major difference from softwares like [ranger](https://github.com/ranger/ranger), [sfm](https://github.com/afify/sfm), [nnn](https://github.com/jarun/nnn), [lf](https://github.com/gokcehan/lf) or [rover](https://github.com/lecram/rover) is that in __frolicd__, letters are not mapped to _actions_. you use letters to write a small part of the directory's name you want to enter it, and once there is only one directory's name that matches, you automatically jump in this directory. in most cases, 2 letters is enough, sometimes 3, rarely more, i'd say (but, of course, it depends a lot on the way you name and organize directories).

why browsing directories like that? because i personally don't need my file browser to do many things, i basically just want an interactive version of `cd` so i don't need to type `cd`, `ls`, `<tab>`, `<space>` and `<enter>` every second. (and i don't want to use `jjjj<enter>` or `/pattern<enter><enter>` neither, because this is to many keystrokes.)

let's say you're in your home directory. you start __frolicd__ and it shows you the content of your current directory:

```
work/      music/    downloads/
books/     films/    backup/
.bashrc    .config   softwares/
```

you want to go somewhere in the `backup` directory. you just type `b` then `a` and here is it: you're now in `backup` directory, without having to press `enter` or `/` or anything. why? because `backup` is the only _directory_ which name contains `ba` (there is another way to match `.bashrc`). then you see the content of `backup` (everytime you enter a directory, its content is printed):

```
oldstuff/    oldbooks/
```

you want to go to `oldstuff/`, and let's say that you press `ld`. there are two directory names that match this pattern. then you have to filter matches until only one is left. here you could enter `s` and that's all, you'll be in `oldstuff`. 

by default, __frolicd__ will only match _directory_ names, in order to reduce the number of multiple name matches, and because i usually want to select a file only at the end of my _promenade_. when you may want to select a file, for example to edit it in neovim (i've made a very minimal [plugin](https://github.com/thjbdvlt/frolicd.nvim) for that), you prefix the selecting pattern by `space`: `<space>ck` matches `mybackup.sql` but not `backup/`.

if you want to match at the beginning of a name, you can use `^`, at the end `$`. as `/` shouldn' be used in filenames, it's an alias for both, depending of its position: `/a` is equivalent to `^a` and `a/` to `a$`.

__frolicd__ also hide hidden files. you can show them by toggling an option (options are set with `![option]`): `!h`. you can also prefix the filtering pattern with `/.`, which will be translated to `^\.`. as it matches only hidden files, the option would be toggled automatically.

a few examples:

- `<space>/ma` matches file `maths.md`
- `ma<space>/in` matches directory `information/`
- `/c/` matches directory `c`
- `/.co` matches directory `.config`

other commands are:

- `.` to go parent directory
- `~` to go home directory
- `,` to create a new file and exit as if it was selected.
- `;` searched recursively through subdirectories using `fzf`.
- `'[a-z]` jump to bookmark
- `+[a-z]` add bookmark

selecting a _file_ will end __frolicd__ session and print the selected file to `stdout`, as if you would have used `enter` (`return`) after having selected a directory, which end the frolicd session and output directory name.

as you can see, __frolicd__ is useless in itself because __it nearly doesn't do anything__ appart from changing directory and `echo`ing directory/file name. hence i never call `frolicd` alone, i only have this alias, which `cd` into `frolicd` output if it's a directory, or edit it with `$EDITOR` if it's a regular file:

```bash
alias r='s=$(frolicd); 
    if test -d "$s"; then 
        cd "$s"; 
    else
        $EDITOR "$s";  # could also be `xdg-open`
    fi'
```

or, if you want to keep __frolicd__ opened:

```bash
alias r='while true; do
    s=$(frolicd)
    read -p "[e]dit? [o]pen? [Q]uit?" action
    if test -d "$s";then
        cd "$s";
fi
    case $action in
        e) $EDITOR "$s";;
        o) xdg-open "$s";;
        *) break;;
    esac
done'
```

a few file management actions are actually available from inside __frolicd__ (so it does not do _nothing_). you call them with `:[action]`. when you chose an action, its name appear in a header, and the next file that you select will become the object of the action. if the action requires two paramater (e.g. `move`), after you have selected the object you can freely use __frolicd__ as before, and once you've found the destination, you enter `:.` and the action will be executed with the current directory as a destination (target).

available actions are:

- `:r` renames (uses `mv`)
- `:d` deletes (uses `rm`)
- `:m` moves   (uses `mv`)
- `:c` copy   (uses `cp`)
- `:.` chose a destination for `:m` and `:c`.
- `:q` exit silently.

dependancies
------------

- [fd](https://github.com/sharkdp/fd)
- optionnal: [fzf](https://github.com/junegunn/fzf)
