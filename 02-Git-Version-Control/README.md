**Basic-level Questions**

1. What is the difference between Git and SVN?

   Answer: Git is a distributed version control system (DVCS) — every clone contains the full repository history and operations are mostly local. SVN (Subversion) is centralized — history and commits are managed on a central server and many operations require network access.

2. What is the difference between `git pull` and `git fetch`?

   Answer: `git fetch` updates your remote-tracking branches (downloads commits and refs) but does not change your working tree or current branch. `git pull` is a convenience for `git fetch` followed by a merge (or a rebase depending on config) into your current branch, which can modify your working tree.

3. What is a "Merge Conflict" and how do you resolve it?

   Answer: A merge conflict occurs when Git cannot automatically reconcile overlapping changes (e.g., the same line edited on two branches). Resolve by inspecting conflict markers in the file, editing to the correct result, then `git add <file>` and `git commit`. Use `git mergetool` or an editor with merge support to simplify the process.

4. What is the difference between `git merge` and `git rebase`?

   Answer: `git merge` combines histories and creates a merge commit, preserving the true chronological history. `git rebase` rewrites commits by moving your branch onto another base to create a linear history. Avoid rebasing commits that have been pushed and shared, since it rewrites history.

5. What is `git stash` and when would you use it?

   Answer: `git stash` temporarily saves (stashes) uncommitted changes from the working directory and index so you can switch branches with a clean tree. Use `git stash pop` to re-apply and remove the stash or `git stash apply` to re-apply without removing it.

6. What is the "Staging Area" (Index) in Git?

   Answer: The index (staging area) is an intermediate place where you prepare changes to include in the next commit. Use `git add` to stage specific files or hunks, and `git commit` to record the staged snapshot.

7. What is `git cherry-pick`?

   Answer: `git cherry-pick <commit>` applies the changes from a specific commit onto your current branch as a new commit. It's useful for backporting a single fix but can create duplicate commits across branches and may require conflict resolution.

8. How do you undo a commit that has already been pushed?

   Answer: Use `git revert <commit>` to create a new commit that undoes the effects of the target commit (safe for shared branches). `git reset --hard` rewrites history and should be avoided on public branches.

9. What is a "Detached HEAD"?

   Answer: A detached HEAD happens when you checkout a commit (not a branch), so HEAD points directly to that commit. New commits in this state are not on a branch and can be lost; create a branch (`git checkout -b <name>`) to keep them.

10. What is the purpose of `.gitignore`?

    Answer: `.gitignore` lists file patterns Git should ignore (not track), such as build artifacts, dependency directories, or local config (e.g., `node_modules/`, `.env`). It keeps the repository clean from environment-specific files.

11. What is `git clone` vs `git fork`?

    Answer: `git clone` copies a repository (including history) to your machine. A "fork" is a copy of a repository under a different user/account on hosting platforms (GitHub/GitLab) used to propose changes via pull/merge requests; after forking you `git clone` your fork.

12. How do you find a bug-introducing commit?

    Answer: Use `git bisect` to perform a binary search over commits by marking known good and bad commits; `git bisect` checks out midpoints until it identifies the offending commit. Automate tests with `git bisect run` if possible.

13. What are Git Hooks?

    Answer: Git hooks are executable scripts placed in `.git/hooks/` that run on repository events (e.g., `pre-commit`, `pre-push`, `post-merge`). They can enforce checks, run linters/tests, or integrate tooling.

14. What is the difference between "Soft Reset" and "Hard Reset"?

    Answer: `git reset --soft <ref>` moves `HEAD` to `<ref>` but leaves the index and working tree unchanged (staged changes remain). `git reset --mixed <ref>` (default) moves `HEAD` and updates the index but leaves working files alone. `git reset --hard <ref>` resets `HEAD`, index, and working tree — discarding uncommitted changes.

15. What is a Pull Request (PR)?

    Answer: A pull request (PR) is a hosted-repository workflow feature for proposing changes: it lets you request that maintainers review, discuss, run CI, and merge your branch into a target branch. PRs facilitate code review and collaboration.

**Intermediate-level Questions**

1. What is the difference between `git reset`, `git revert`, and `git checkout`?

   Answer:

`git reset`: Moves the current branch pointer to a previous commit and can update the index and working tree depending on flags; it rewrites history when used to change commits (dangerous on shared branches).

`git revert`: Creates a new commit that reverses the changes introduced by a previous commit—safe for shared branches because it preserves history.

`git checkout`: Switches `HEAD` to a different branch or commit. When used to switch branches it updates the working tree; when checking out a commit it can create a detached HEAD.

2. What is `git reflog` and why is it a lifesaver?

   Answer: `git reflog` records updates to `HEAD` and branch refs (all moves, even those not reachable from refs). It lets you find lost commits, recover deleted branches, or undo mistakes like `reset --hard` by exposing recent SHA-1 values.

3. What is a "Squash" in Git?

   Answer: Squashing combines multiple commits into a single commit (commonly via interactive rebase `git rebase -i`) to produce a cleaner history before merging.

4. Explain the "GitFlow" workflow.

   Answer: GitFlow is a branching model with roles:

- `main`/`master`: production-ready code.
- `develop`: integration branch for features.
- `feature/*`: short-lived branches for features.
- `release/*`: prepare a release (final fixes, versioning).
- `hotfix/*`: quick fixes applied to `main` and merged back to `develop`.

5. What is the difference between a "Fast-forward" merge and a "Three-way" merge?

   Answer:

Fast-forward: If target branch has no new commits, Git simply moves the branch pointer forward to the feature tip (no merge commit).

Three-way: When both branches have diverged, Git creates a merge commit that ties both histories together using the common ancestor.

6. What is `git commit --amend`?

   Answer: `git commit --amend` modifies the most recent commit (message and/or content). It rewrites history for that commit, so avoid amending commits that have already been shared.

7. What are "Dangling Commits"?

   Answer: Commits not referenced by any branch or tag (e.g., after `reset` or an orphaned rebase). They are accessible via `git reflog` or `git fsck --lost-found` until garbage collection (`git gc`) prunes them.

8. How do you find which developer changed a specific line in a file?

   Answer: Use `git blame <file>` to show the commit, author, and timestamp for each line. For a more readable history, `git log -L` or `git pickaxe` (`git log -S`/`-G`) can help trace changes.

9. What is `git submodule`?

   Answer: `git submodule` embeds another repository at a specific commit inside a parent repo. It keeps histories separate and requires explicit updates (`git submodule update --init --recursive`) to fetch or sync the nested repo.

10. What happens if you delete a branch before merging it?

    Answer: The branch ref is removed; its commits become unreachable (dangling) but still recoverable for a time via `git reflog` or the commit SHA. Force-delete uses `git branch -D`; deleted commits are eventually pruned by `git gc` if not reachable.

**Expert-level Questions**

1. What are the four main types of objects in Git?

   Answer: Git stores data as four core object types in a content-addressable database:

- Blob: stores raw file content (binary-safe), identified by SHA-1/SHA-256 of the content.
- Tree: represents a directory; it maps filenames to blob or subtree object IDs and file modes.
- Commit: points to a tree and records metadata (author, committer, message, parent(s)), forming history.
- Tag: an object that names another object (often a commit). Annotated tags include tagger metadata and a message; lightweight tags are just refs.

2. How does Git handle storage? Does it store "diffs" or "snapshots"?

   Answer: Conceptually Git stores snapshots — each commit references a tree that describes the project state. Practically, storage is optimized: unchanged files reference the same blob, and objects are packed into `packfiles` which use delta compression (i.e., diffs between objects) to save space. So Git exposes snapshots but stores objects efficiently using delta packing.

3. What is a "Git Mirror" (`--mirror`)?

   Answer: `git clone --mirror` creates a bare clone that mirrors all refs (branches, tags, and remote refs) and also copies remote configuration for push/pull. It's meant for repository migration or exact mirrored backups; pushing a mirror (`git push --mirror`) will update refs exactly on the target (dangerous if done without care).

4. What is `git rev-parse`?

   Answer: `git rev-parse` is a plumbing utility that resolves and formats refs and revisions. Common uses: convert human-friendly names to commit IDs (`git rev-parse HEAD`), validate refs (`--verify`), output short hashes (`--short`), or get symbolic names (`--abbrev-ref HEAD`). It's heavily used in scripts and hooks to canonicalize input.

5. Explain "Atomic Commits" and why they matter in DevOps.

   Answer: An atomic commit encapsulates a single logical change (code + tests + migration where applicable) so it can be applied, reverted, or bisected cleanly. Benefits for DevOps:

- Safer rollbacks and reverts (single unit of change).
- Faster, more precise `git bisect` runs and clearer blame attribution.
- Easier code review and automated testing (CI can target one behavior change).
- Better coordination for database/schema changes: pair schema migrations with code in a single deployable unit, or follow deployment strategies (feature flags, transactional migrations) when atomicity across systems isn't possible.

Practical advice: keep commits focused, include tests, and document migration steps for infra or DB changes; use feature flags for long-running or multi-step rollouts.
