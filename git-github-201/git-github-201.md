# Git/GitHub 201

Frameworks Lunch & Learn

### Git vs GitHub

Git (2005) was originally developed for Linux kernel maintenance.

GitHub (2008) is a _hosting platform_ for git repositories with its own UI and additional concepts (like pull requests!).

### Configuring git to only track your own branches
```bash
git branch -r | xargs -L1 git branch -Dr
git config --unset-all remote.origin.fetch
git config --add remote.origin.fetch "+refs/heads/master:refs/remotes/origin/master" # or main
git config --add remote.origin.fetch "+refs/heads/ntarrh/*:refs/remotes/origin/ntarrh/*" # your username here
```

Verify with:
```bash
git config --get-all remote.origin.fetch
git fetch --all -v
```

### ...and collaborating on a branch that isn't yours
```bash
git config --add remote.origin.fetch "+refs/heads/some-branch-name:refs/remotes/origin/some-branch-name"
git fetch origin some-branch-name
git checkout some-branch-name
```

### Commit references
```bash
# `main` refers to a particular commit
$ git rev-parse --short main
9631e24

$ ls .git/refs
heads   remotes stash   tags

$ tree .git/refs/heads
.git/refs/heads
├── dogor
│   └── PIEPMO-1409-feature-flags-fail-option
├── master # or main, or whatever
├── ntarrh
│   ├── add-gns-config-options
│   ├── dynamic-protobuf-namespace
│   ├── fix-proto-test-helper
│   ├── health-status-reporter
│   └── remove-gns-zk
└── whua
    └── 23-04-05
        └── add-healthcheck

$ tree .git/refs/remotes
.git/refs/remotes
└── origin
    ├── HEAD
    ├── acrutchfield
    │   └── puma.collector
    ├── ccun
    │   └── IAM-1171
    │       ├── envoy-diff-middleware
    │       └── logging-x-sq-auth-context-mismatches
    ├── cheister
    │   ├── bump-square-rb
    │   └── rails7
    ├── clean-branch
    ├── cogan
    │   └── fix_admin_console_system_stack_error_WIP
    ├── dannys-dannys
    │   └── sq-metrics-use-rspec-matcher-in-find_metric-kochiku-canary
    ├── dogor
    │   ├── 22-12-01
    │   │   └── add-otel-propagation
  [...]

# `HEAD` is a special ref to the current commit
$ git rev-parse --short HEAD
9631e24

# we're currently looking at the latest commit on the `main` branch
$ git rev-parse --abbrev-ref HEAD
main

$ git cat-file -p 9631e24
tree 287a1c972eab647366c74d37408f9722c0ee1762
parent 06068b186b0d32741cac99f9c6a501f8a5f2352d
author Nathan Tarrh <ntarrh@squareup.com> 1682527354 -0700
committer Nathan Tarrh <ntarrh@squareup.com> 1682527354 -0700

checkin

# `~` is a relative reference
$ git rev-parse --short HEAD~
06068b1

# `n` is number of parents, a bare `~` means the first parent
$ git rev-parse --short HEAD~1
06068b1

         HEAD~2    HEAD~1     HEAD~      HEAD
┌───┐     ┌───┐     ┌───┐     ┌───┐     ┌───┐
│ A │ <── │ B │ <── │ D │ <── │ E │ <── │ G │
└───┘     └───┘     └───┘     └───┘     └───┘
            ∧           (merge) │
            │                   │
            │ (branch)          │
          ┌───┐                 │
          │ C │ <───────────────┘
          └───┘
```


### Interactive rebase
```
$ git rebase -i HEAD~4
pick 704e06c up mem limit
pick 19a1859 config aws
pick a8d97cf Update config/rubyexemplar-aws.yaml
pick b518a59 Bundle update 3/16
pick 788d7a2 Update sq-multipass to v7.0.1
pick 4f1976d rails generate home controller
pick d60536a remove spec test
pick 0f4de01 appease the rubocops
pick 97f9889 feedback

# Rebase 9aaac9d..50105a5 onto 9aaac9d (9 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup [-C | -c] <commit> = like "squash" but keep only the previous
#                    commit's log message, unless -C is used, in which case
#                    keep only this commit's message; -c is same as -C but
#                    opens the editor
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
#         create a merge commit using the original merge commit's
#         message (or the oneline, if no original merge commit was
#         specified); use -c <commit> to reword the commit message
# u, update-ref <ref> = track a placeholder for the <ref> to be updated
#                       to this position in the new commits. The <ref> is
#                       updated at the end of the rebase
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
```

### The reflog
```bash
$ git reflog
6c14b1be (HEAD -> master, origin/master, origin/HEAD) HEAD@{0}: pull --prune: Fast-forward
5185dd18 HEAD@{1}: pull --prune: Fast-forward
114a1fb0 HEAD@{2}: checkout: moving from 1df4e1498cae794f6f5c537a070d090eb6a3d90f to master
1df4e149 HEAD@{3}: checkout: moving from whua/23-04-05/add-healthcheck to 1df4e1498cae794f6f5c537a070d090eb6a3d90f
a5cb1c40 (whua/23-04-05/add-healthcheck) HEAD@{4}: checkout: moving from 1df4e1498cae794f6f5c537a070d090eb6a3d90f to whua/23-04-05/add-healthcheck
1df4e149 HEAD@{5}: checkout: moving from whua/23-04-05/add-healthcheck to 1df4e1498cae794f6f5c537a070d090eb6a3d90f
a5cb1c40 (whua/23-04-05/add-healthcheck) HEAD@{6}: checkout: moving from master to whua/23-04-05/add-healthcheck
114a1fb0 HEAD@{7}: checkout: moving from whua/23-04-05/add-healthcheck to master
5ea66216 HEAD@{8}: checkout: moving from master to whua/23-04-05/add-healthcheck
114a1fb0 HEAD@{9}: pull --prune: Fast-forward
28c470cd HEAD@{10}: checkout: moving from ntarrh/add-gns-config-options to master
43e2898c (origin/ntarrh/add-gns-config-options, ntarrh/add-gns-config-options) HEAD@{11}: commit: Update changelogs
f2ba42a2 HEAD@{12}: rebase (finish): returning to refs/heads/ntarrh/add-gns-config-options
f2ba42a2 HEAD@{13}: rebase (start): checkout origin/master
f2ba42a2 HEAD@{14}: rebase (finish): returning to refs/heads/ntarrh/add-gns-config-options
f2ba42a2 HEAD@{15}: rebase (pick): Let users configure `gns.only_with_label`
d72611ca HEAD@{16}: rebase (pick): Configure Sq::GNS with GemConfig
28c470cd HEAD@{17}: rebase (start): checkout master
7b3e5cf0 HEAD@{18}: rebase (finish): returning to refs/heads/ntarrh/add-gns-config-options
7b3e5cf0 HEAD@{19}: rebase (pick): Let users configure `gns.only_with_label`
af6154cb HEAD@{20}: rebase (fixup): Configure Sq::GNS with GemConfig
6438b050 HEAD@{21}: rebase (pick): Configure Sq::GNS with GemConfig
68c37830 HEAD@{22}: rebase (pick): LSC: Remove former employees from OWNERS.yaml and MODULES.yaml files
cd398d9b HEAD@{23}: rebase (start): checkout HEAD~4
f0188b1f HEAD@{24}: commit: asdf
7bd076bc HEAD@{25}: commit: Let users configure `gns.only_with_label`
2fef3f6f HEAD@{26}: commit: Configure Sq::GNS with GemConfig
28c470cd HEAD@{27}: checkout: moving from master to ntarrh/add-gns-config-options
```

### Commit ranges

```bash
$ git shortlog --no-merges sq-common/v7.1.0..sq-common/v7.2.0
Andrew Lazarus (1):
      use proto package names instead of generated class names

Bob Lail (2):
      LSC: Remove former employees from OWNERS.yaml and MODULES.yaml files
      LSC: Remove former employees from OWNERS.yaml and MODULES.yaml files

Chris Heisterkamp (2):
      Update Ruby versions used in CI
      [sq-lint] Update rubocop to 1.50.0, rubocop-rails to 2.19.0 and rubocop-rspec to 2.19.0

Cogan Noll (2):
      Update contributing documentation for testing changes in a real app (#1464)
      Fix bug in Application Config tab in admin console (#1463)

David (5):
      Support has_unsafe_defaults for LaunchDarkly to Fail Startup When No Defaults Permitted (#1451)
      [framework] Emit Metrics for `has_unsafe_defaults` behavior with LaunchDarkly (#1468)
      [framework] Fix has_unsafe_defaults to set true for failing apps (#1472)
      [framework] Enable Configurable Puma Worker Boot Timeout (#1473)
      Release sq-common v.7.2.0, sq-connect_client  v.1.2.1, sq-kafka_feeds, v.1.0.2, sq-launch_darkly v.1.5.0, sq-lint v.4.3.1.50.0, sq-metrics v.7.8.2, sq-proto v.6.7.4, sq-rpc v.2.10.3 (#1479)

David Amar (1):
      Add optional Auth header to RPC tab in admin console

Matt Dickenson (2):
      Don't set resource if active_trace is nil
      Add changelog message

Sam W (1):
      Don't require the 'opentelemetry_enabled' field anymore (#1466)

Sam Westerman (1):
      [RUBY-396] Update gemspecs for ruby service container

cheister (1):
      Fix protobuf references (#1482)

```

### Good commit messages and why they matter
https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html

### Why is it called a "pull request"?
https://github.com/torvalds

You're asking the code owner to "pull" (`git fetch` and then `git merge`, aka `git pull`) your changes into their repository.

### Resources

https://www.atlassian.com/git/tutorials

https://git-scm.com/book/en/v2
