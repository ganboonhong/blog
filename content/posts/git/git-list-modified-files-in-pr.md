---
title: "List Modified Files by Filtering Keyword in git Commit Messages"
description: "List Modified Files by Filtering Keyword in git Commit Messages"
summary: "I was pretty suprised that when I was looking for a git command that can list out all the modified files in git commits that contain certain string. Then I found that there's no such a command out of the box. So I start searching for different commands and put those pieces together." # For the post in lists.
date: "2022-09-17"
author: "Ben"
thumbnail: "/blog/images/search-pr-by-keyword.png"

tags:
    - git
---

{{< imgborder "images/search-pr-by-keyword.png" "Searching PRs by keyword" "80%">}}

{{% notice warning "Problem" %}}
As you can see in the above screenshot, my last commit in this PR deliberately fails the CI flow in Drone and we can see the "All checks have failed" warning. But this PR is still mergable (even by a non-admin user).
{{% /notice %}}


{{% notice warning "Problem" %}}
I was pretty suprised when I was looking for a git command that can list out all the modified files in git commits that contain certain string in commit messages. <br>
Then I found that there's no such a command out of the box.
So I start searching for different commands and put those pieces together.
{{% /notice %}}

I have the following restrictions on company's working environment:

- only `git` command is available
- `gh` command is not available

An alternative would be searching the keyword using GUI then open each commit hashes to get the modified files manually.
If you've only a few commits, this approach could work pretty well.
But it's a time consuming task if you got a lot commits to deal with.
So doing it in CLI way would be more efficient.

{{% notice tip "Solution" %}}


```shell {hl_lines=["1-4"]}
git log --pretty=format:"%h %ae %ar %s" | \
grep 'ticket-1054' | \
grep 'user@example.com' | \
grep -v "Merge pull request"

53fd98e9 user@example.com 26 hours ago 	fix: ticket-1054 gate entries feature (case 2904)
ff96c35f user@example.com 28 hours ago  fix: ticket-1054 gate entries feature
b80f5a95 user@example.com 2  days ago   fix: ticket-1054 gate entries feature (case 2902, 2903)
fa82d145 user@example.com 9  days ago   fix: ticket-1054 gate entries feature (case 2891)
5e1aadb0 user@example.com 9  days ago   fix: ticket-1054 gate entries feature (translation)
400afa9a user@example.com 2  weeks ago  fix: ticket-1054 gate entries feature (case 2891)
d3714b22 user@example.com 2  weeks ago  fix: ticket-1054 gate entries feature (case 2891)
6e3958e9 user@example.com 2  weeks ago  fix: ticket-1054 gate entries feature (case 2889, 2890)
12ea69f9 user@example.com 2  weeks ago  fix: ticket-1054 gate entries feature (case 2888)
f8ce5a2c user@example.com 2  weeks ago  fix: ticket-1054 gate entries feature (case 2887)
97c0d8c5 user@example.com 2  weeks ago  fix: ticket-1054 gate entries feature (case 2884, 2885, 2886, 2887)
f8f40bcf user@example.com 2  weeks ago  fix: ticket-1054 gate entries feature (case 2884, 2885, 2886, 2887)
3531096f user@example.com 3  weeks ago  feat: ticket-1054 gate entries feature (translation)
cab6e773 user@example.com 6  weeks ago  fix: ticket-1054 gate entries feature (translation)
a4762b29 user@example.com 6  weeks ago  fix: ticket-1054 gate entries feature (translation)
f7473d05 user@example.com 6  weeks ago  fix: ticket-1054 gate entries feature (case 2816)
18339de1 user@example.com 6  weeks ago  fix: ticket-1054 gate entries feature (case 2816)
4ad4801c user@example.com 7  weeks ago  fix: ticket-1054 gate entries feature (case 2816)
2fee90ac user@example.com 7  weeks ago  fix: ticket-1054 gate entries feature (case 2815, 2816)
e8f11317 user@example.com 7  weeks ago  fix: ticket-1054 gate entries feature (case 2812, 2814)
81cab87c user@example.com 10 weeks ago  feat: ticket-1054 gate entries feature
```
{{% /notice %}}


| Command | Explanation |
| ------------------------------ | --------------------------------------------------- |
| **git log** | I use git log command to extract the commit messages [Docs](https://git-scm.com/docs/git-log)|
| **git log --pretty %h** | Abbreviated commit hash [Docs](https://git-scm.com/docs/git-log#Documentation/git-log.txt-emhem)|
| **git log --pretty %ae** | Author email [Docs](https://git-scm.com/docs/git-log#Documentation/git-log.txt-emaeem)|
| **git log --pretty %ar** | Author date, relative [Docs](https://git-scm.com/docs/git-log#Documentation/git-log.txt-emarem)|
| **git log --pretty %s** | Subject(first line of the commit message) [Docs](https://git-scm.com/docs/git-log#Documentation/git-log.txt-emsem)|
| **grep 'ticket-1054'** | ticket-\* is branch name in commit message, which is a my custom keyword in this case. It can be any keyword depends on your needs. |
| **grep 'user@example.com'** | filter by email address |
| `bb**grep -v 'Merge pull request'** | exclude merge pull request commits (commit of a pull request merge won't show modified files in `git diff-tree` command later)|



Then we use `git diff-tree --no-commit-id --name-only -r <commit-hash>` to loop through all commits to list out the modified files.


```shell {hl_lines=[1]}
git diff-tree --no-commit-id --name-only -r 53fd98e9 

api/queries_pr.go
api/query_builder.go
pkg/cmd/pr/checks/aggregate.go
pkg/cmd/pr/checks/checks.go
pkg/cmd/pr/checks/checks_test.go
pkg/cmd/pr/checks/fixtures/allPassing.json
pkg/cmd/pr/checks/fixtures/onlyRequired.json
pkg/cmd/pr/checks/fixtures/someFailing.json
pkg/cmd/pr/checks/fixtures/somePending.json
pkg/cmd/pr/checks/fixtures/someSkipping.json
pkg/cmd/pr/checks/fixtures/withStatuses.json
```

| Command | Explanation |
| ------------------------------ | --------------------------------------------------- |
| **git diff-tree** | Show modified files [Docs](https://git-scm.com/docs/git-diff-tree)|
| **git diff-tree --no-commit-id** | Hide commit ID output [Docs](https://git-scm.com/docs/git-diff-tree#Documentation/git-diff-tree.txt---no-commit-id)|
| **git diff-tree --name-only** | Show only names of changed files.  [Docs](https://git-scm.com/docs/git-diff-tree#Documentation/git-diff-tree.txt---name-only)|
| **git diff-tree -r** | recurse into sub-trees (show full path instead of directory only) [Docs](https://git-scm.com/docs/git-diff-tree#Documentation/git-diff-tree.txt--r)|

