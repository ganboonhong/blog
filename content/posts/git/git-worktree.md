---
title: "Utilize git-worktree When Handling Urgent Ticket"
description: "git-worktree can be very helpful when you want to work on another branch and leave current branch untouched"
summary: "Have you ever been in a situation that, you're halfway to complete a feature and interrupted by an urgent ticket? You've modified tons of files and you need to put the modified files somewhere so that you can start working on the urgent ticket." # For the post in lists.
date: "2020-03-27"
author: "Ben"
thumbnail: "/images/git/worktree-before.png"

tags:
    - git
---
{{< imgborder "images/git/worktree-before.png" "Modified files" "60%">}}

{{% notice warning "Problem" %}}
Have you ever been in a situation that, you're halfway to complete a feature and interrupted by an urgent ticket? You've modified tons of files and you need to put the modified files somewhere so that you can start working on the urgent ticket.
{{% /notice %}}


Of course, one of the solutions to handle this situation might be using `git stash` command to stash your changes. As titled, I'm not going to discuss this approach here. Instead, I'm going to use `git worktree` command ([official doc](https://git-scm.com/docs/git-worktree)) to tackle this issue.

{{% notice tip "Solution" %}}
```shell
git worktree add <new_path> -b <new_branch>
```
{{% /notice %}}

Let's create a new worktree by using `git worktree add <new_path> -b <new_branch>`
{{< imgborder "images/git/worktree-execute.png" "Create a new worktree by using `git worktree add`" "70%">}}

As you can see, new worktree is located in new directory with new git branch. You might think that you could also achieve the same goal by creating a new directory and cloning the repository again.

Admittedly, the cloning approach works too, but the disk usage of directories are different. (Of course, there are differences between two approaches other than disk usage.)

{{% notice info "Disk Usage" %}}
New worktree only took around 19% of the original one. For project with larger size, the difference in size would be more significant. Also, it took more time to clone a large repository if you prefer the cloning approach.
{{% /notice %}}


{{< imgborder "images/git/worktree-disk-usage.png" "Disk usage of the linked worktree is significantly less than the main worktree" "80%">}}

{{% notice info "Running Time Consuming Test Cases" %}}
Another situation is that If you must run test cases on local that take quite a long time, you may apply the above strategy to avoid hanging there do nothing. Hope the above trick may help you to increase your productivity.
{{% /notice %}}

Happy coding!
