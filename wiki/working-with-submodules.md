# working with submodules

#### clone and init repository 

```bash
git clone --recursive https://github.com/ethereum/webthree-umbrella
```

#### update submodules from remote

```bash
git submodule update --init --remote . # to update all submodules
```

```bash
git submodule update --init --remote submodule_name # to update one submodule
```

#### pull repository && submodules

```bash
git pull
git submodule update --init .
```

#### pull changes from branch to submodule

```bash
cd submodule_name
git pull origin branch_to_pull
git push origin HEAD:develop # let's assume that our submodule follows develop branch
cd ..
git add submodule_name
git commit -m 'my feature'
```

#### resolve submodule conflicts

if after merge with can see something like this

```
Unmerged paths:
  (use "git add <file>..." to mark resolution)

  both modified:   submodule_dir
```

and `git diff` shows us something like:

```
- Subproject commit 70d142c96846eba03404873823ca251c3c6be242
 -Subproject commit 49e9717de5f7293cb0aec4a42a31a3c595a39b19
++Subproject commit 0000000000000000000000000000000000000000
```

we need to reset our submodule to one of the branches

```bash
git reset branch_name -- submodule_dir
git commit
```

#### change submodule branch permanently (in .gitmodules)

```bash
vim .gitmodules # edit stuff that you need
git submodule sync
git deinit submodule_dir # deinit submodule that you editted. or '.' to deinit all
git submodule update --init --remote submodule_dir # pull remote into submodule
```