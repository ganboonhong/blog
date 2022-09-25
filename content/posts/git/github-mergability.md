---
title: "GitHub PR mergeability"
description: "git-worktree can be very helpful when you want to work on another branch and leave current branch untouched"
summary: "Have you ever been in a situation that, you're halfway to complete a feature and interrupted by an urgent ticket? You've modified tons of files and you need to put the modified files somewhere so that you can start working on the urgent ticket." # For the post in lists.
date: "2020-03-26"
author: "Ben"
thumbnail: "/images/github/pr-merge-not-block.png"

tags:
    - git
---

{{< imgborder "images/github/pr-merge-not-block.png" "No rule protection on pull request, PR that failed in CI is still mergable" "80%">}}

{{% notice warning "Problem" %}}
As you can see in the above screenshot, my last commit in this PR deliberately fails the CI flow in Drone and we can see the "All checks have failed" warning. But this PR is still mergable (even by a non-admin user).
{{% /notice %}}

Most of the time, we want to block pull requests that don't pass status checks from merging into master branch. GitHub provides a great feature called **Branch protection rule** to achieve this goal ([doc](https://help.github.com/en/github/administering-a-repository/defining-the-mergeability-of-pull-requests)).

{{% notice tip "Solution" %}}
**Create a Branch Protection Rule** <br>
This function is located in repository Settings > Code and automation > Branches > Add rule
{{% /notice %}}

{{< imgborder "images/github/pr-merge-rules-location.png">}}

Let's create the rule as below:
{{< imgborder "images/github/pr-merge-create-rule.png">}}

Notice that the **Branch name pattern** field, the value shouldn't contain any white space. Otherwise, you'll get a invalid rule error message.

Now we have a rule to block PR with failing status.

{{< imgborder "images/github/pr-merge-block.png" "" "80%">}}


{{% notice note "Administrator still can merge PR even the checks have failed" %}}
By checking the `Include administrators` option, the restrictions will be applied to administrators too.
{{% /notice %}}
