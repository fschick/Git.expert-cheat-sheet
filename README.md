# Graph anatomy

 Git is based on a data structure concept called "Directed Acyclic Graph" (DAG) (German: "gerichteter azyklischer Graph")

```mermaid
---
title: Git commits (directed acyclic graph)
---
%%{init: { 'theme': 'base', 'themeVariables': { 'fontSize': '16px', 'commitLabelFontSize': '14px'} } }%%
gitGraph TB:
  commit id: "M00 (root)"
  branch "feature a" order: 0
  checkout "feature a"
  commit id: "FA01 (M00)"
  branch "feature b"
  checkout "feature b"
  commit id: "FB01 (FA01)"
  checkout "feature a"
  commit id: "FA02 (FA01)"
  checkout main
  commit id: "M01 (M00)"
```

# Commit anatomy

A commit encapsulates the changes to the files since the last commit. It records what was added, removed, or modified in the project.

```mermaid
classDiagram
  class Commit {
    SHA
    Tree | SHA
    Commit | Message
    Commit | Parent
    Author | Name, E-Mail, Timestamp
    Comitter | Name, E-Mail, Timestamp
 }
 
 class Merge_Commit {
    SHA
    Tree | SHA
    Commit | Message
    Commit | ParentA, ParentB
    Author | Name, E-Mail, Timestamp
    Comitter | Name, E-Mail, Timestamp
 }
```

The SHA of a commit is build from the following information

```
tree <TREE-SHA>
parent <PARENT1-SHA>   # Only if there are parent commits
parent <PARENT2-SHA>   # Only if a merge commit
author <AUTHOR-NAME> <AUTHOR-EMAIL> <AUTHOR-TIMESTAMP>
committer <COMMITTER-NAME> <COMMITTER-EMAIL> <COMMITTER-TIMESTAMP>

<COMMIT-MESSAGE>
```

# Node, Commit, SHA

A node, called a 'commit' in git, represents a specific state or snapshot of the repository. A node is represented by its SHA.

```mermaid
---
title: Nodes
---
%%{init: { 'theme': 'base', 'themeVariables': { 'fontSize': '16px', 'commitLabelFontSize': '14px'} } }%%
gitGraph TB:
    commit id: "M00 (root)"
    commit id: "M01 (M01)"
    commit id: "M02 (M01)" type: HIGHLIGHT
```

# Tag, Branch, Stash

Technically, a tag, a branch, and a stash are all the same: 

An alias (pointer) to a  particular snapshot (commit, identified by its SHA) of the repository

```mermaid
---
title: tag, branch, stash
---
%%{init: { 'theme': 'base', 'gitGraph': { 'mainBranchOrder': 1 }, 'themeVariables': { 'fontSize': '16px', 'commitLabelFontSize': '14px', 'tagLabelFontSize': '14px' } } }%%
gitGraph TB:
    commit id: "M00 (root)"
    branch feature order: 2
    commit id: "F01 (M00)" type: HIGHLIGHT
    checkout main
    commit id: "M01 (M00)"  tag: "1.0.0" type: HIGHLIGHT
    branch stash order: 0
    commit id: "S01 (M00)" type: HIGHLIGHT
    checkout main
    commit id: "M02 (M01)"
```

But each serves a different purpose: Tags mark fixed points in history, branches track ongoing development and stashes temporarily save uncommitted changes

# Pull

A `git pull` internally fetches changes from remote (`git fetch`) and integrate (merge) them afterwards into your local branch (`git merge`).

```mermaid
---
title: Before pull
---
%%{init: { 'theme': 'base', 'gitGraph': { 'mainBranchName': 'remote', 'parallelCommits': true }, 'themeVariables': { 'fontSize': '16px', 'commitLabelFontSize': '14px'} } }%%
gitGraph TB:
    commit id: "M00 (root)"
    branch local
    commit id: "M01 (M00) "
    checkout remote
    commit id: "M01 (M00)"
    commit id: "M02 (M01)" type: HIGHLIGHT
```

```mermaid
---
title: Finalized pull
---
%%{init: { 'theme': 'base', 'gitGraph': { 'mainBranchName': 'remote', 'parallelCommits': true }, 'themeVariables': { 'fontSize': '16px', 'commitLabelFontSize': '14px'} } }%%
gitGraph TB:
    commit id: "M00 (root)"
    branch local
    commit id: "M01 (M00) "
    commit id: "M02 (M01) " type: HIGHLIGHT
    checkout remote
    commit id: "M01 (M00)"
    commit id: "M02 (M01)" type: HIGHLIGHT
```
# Merge (fast-forward)

Merge branch `feature` into `main` (using `fast-forward` strategy)

```mermaid
---
title: Before merge
---
%%{init: { 'theme': 'base', 'themeVariables': { 'fontSize': '16px', 'commitLabelFontSize': '14px'} } }%%
gitGraph TB:
    commit id: "M00 (root)"
    branch feature
    commit id: "F01 (M00)" type: HIGHLIGHT
```

```mermaid
---
title: Finalized merge (visible & physical)
---
%%{init: { 'theme': 'base', 'gitGraph': { 'mainBranchName': 'main/feature', 'parallelCommits': true }, 'themeVariables': { 'fontSize': '16px', 'commitLabelFontSize': '14px'} } }%%
gitGraph TB:
    commit id: "M00 (root)"
    branch feature
    commit id: "F01 (M00)" type: HIGHLIGHT
    checkout "main/feature"
    commit id: "F01 (M00) " type: HIGHLIGHT
```

Technically, the pointer to the latest commit of `main` branch is set to the latest commit of `feature` branch.

# Merge (recursive)

Merge branch `feature` into `main` (using `recurive` strategy)

```mermaid
---
title: Before merge
---
%%{init: { 'theme': 'base', 'themeVariables': { 'fontSize': '16px', 'commitLabelFontSize': '14px'} } }%%
gitGraph TB:
    commit id: "M00 (root)"
    branch feature
    commit id: "F01 (M00)" type: HIGHLIGHT
    commit id: "F02 (F01)" type: HIGHLIGHT
    checkout main
    commit id: "M01 (M00)"
```

```mermaid
---
title: Finalized merge (visible)
---
%%{init: { 'theme': 'base', 'themeVariables': { 'fontSize': '16px', 'commitLabelFontSize': '14px' } } }%%
gitGraph TB:
    commit id: "M00 (root)"
    branch feature
    commit id: "F01 (M00)" type: HIGHLIGHT
    commit id: "F02 (F01)" type: HIGHLIGHT
    checkout main
    commit id: "M01 (M00)"
    commit id: "F01 (M00) " type: HIGHLIGHT
    commit id: "F02 (F01) " type: HIGHLIGHT
    merge feature id: "Merge (M01, F01)"
    commit id: "M03 (M02)"
```

```mermaid
---
title: Finalized merge (physical)
---
%%{init: { 'theme': 'base', 'themeVariables': { 'fontSize': '16px', 'commitLabelFontSize': '14px'} } }%%
gitGraph TB:
    commit id: "M00 (root)"
    branch feature
    commit id: "F01 (M00)" type: HIGHLIGHT
    commit id: "F02 (F01)" type: HIGHLIGHT
    checkout main
    commit id: "M01 (M00)"
    merge feature id: "Merge (M01, F01)"
    commit id: "M03 (M02)"
```

Technically

* Changes to files of **all** commits on `feature` branch are applied to `main` branch
* merge conflicts are resolved (either automatically or by user)
* a **single** merge commit with 2 parents is created on branch `main`

# Cherry picking

Cherry pick commits `M01` and `M02` from `main` to `feature` branch

```mermaid
---
title: Before cherry pick
---
%%{init: { 'theme': 'base', 'themeVariables': { 'fontSize': '16px', 'commitLabelFontSize': '14px'} } }%%
gitGraph TB:
    commit id: "M00 (root)"
    branch feature
    commit id: "F01 (M00)"
    checkout main
    commit id: "M01 (M00)" type: HIGHLIGHT
    commit id: "M02 (M01)" type: HIGHLIGHT
```

```mermaid
---
title: Finalized cherry pick
---
%%{init: { 'theme': 'base', 'themeVariables': { 'fontSize': '16px', 'commitLabelFontSize': '14px', 'tagLabelFontSize': '14px'}, 'gitGraph': {'rotateTagLabel': false} } }%%
gitGraph TB:
    commit id: "M00"
    branch feature
    commit id: "F01 (M00)"
    checkout main
    commit id: "M01 (M00)" type: HIGHLIGHT
    commit id: "M02 (M01)" type: HIGHLIGHT
    checkout feature
    commit id: "M01' (F01)" type: HIGHLIGHT
    commit id: "M02' (M01')" type: HIGHLIGHT
```

Changes to files of **individual** commits on branch `main` are replayed, merged and committed as **individual** commits on branch `feature`.

It's the same as if changes of individual commits were made and committed by a user.

# Rebase

Rebase branch `feature` onto `main`

```mermaid
---
title: Before rebase
---
%%{init: { 'theme': 'base', 'themeVariables': { 'fontSize': '16px', 'commitLabelFontSize': '14px'} } }%%
gitGraph TB:
    commit id: "M00 (root)"
    branch feature
    commit id: "F01 (M00)"
    commit id: "F02 (F01)"
    checkout main
    commit id: "M01 (M00)"
    commit id: "M02 (M01)"
```

```mermaid
---
title: Finalized rebase
---
%%{init: { 'theme': 'base', 'themeVariables': { 'fontSize': '16px', 'commitLabelFontSize': '14px'} } }%%
gitGraph TB:
    commit id: "M00 (root)"
    commit id: "M01 (M00)"
    commit id: "M02 (M01)"
    branch "feature"
    commit id: "F01' (M01)"
    commit id: "F02' (F01')"
```

Internally (schematic, real code works slightly different)

* a temporary branch `feature_tmp` is created on commit `M02` of `main` branch
* the commits `F01` and `F02` are **cherry-picked** from `feature` branch into `feature_tmp`
* the `feature` branch is deleted
* the branch `feature_tmp` is renamed to `feaure`

# Squash

Squash the commits `M01` and `M02` to a single commit

```mermaid
---
title: Before squash
---
%%{init: { 'theme': 'base', 'gitGraph': { 'mainBranchName': 'feature' }, 'themeVariables': { 'fontSize': '16px', 'commitLabelFontSize': '14px'} } }%%
gitGraph TB:
    commit id: "M00 (root)"
    commit id: "M01 (M00)"
    commit id: "M02 (M01)"
```

```mermaid
---
title: Finalized squash
---
%%{init: { 'theme': 'base', 'gitGraph': { 'mainBranchName': 'feature' }, 'themeVariables': { 'fontSize': '16px', 'commitLabelFontSize': '14px'} } }%%
gitGraph TB:
    commit id: "M00 (root)"
    commit id: "M0102 (M00)"
```

Internally (schematic, real code works slightly different)

* a temporary branch `main_tmp` is created on commit `M00` of `main` branch
* changes to files of **all** commits to squash are applied to `main_tmp` branch
* changes are committed as **single** commit `M0102`
* the `main` is deleted
* the branch `main_tmp` is renamed to `main`

# History rewriting

Rearrange and / or squash individual commits on a branch

```mermaid
---
title: Before history rewriting
---
%%{init: { 'theme': 'base', 'gitGraph': { 'mainBranchName': 'feature' }, 'themeVariables': { 'fontSize': '16px', 'commitLabelFontSize': '14px'} } }%%
gitGraph TB:
    commit id: "M00 (root)"
    commit id: "M01 (M00)"
    commit id: "M02 (M01)"
    commit id: "M03 (M02)"
```

```mermaid
---
title: Before history rewriting
---
%%{init: { 'theme': 'base', 'gitGraph': { 'mainBranchName': 'feature' }, 'themeVariables': { 'fontSize': '16px', 'commitLabelFontSize': '14px'} } }%%
gitGraph TB:
    commit id: "M00 (root)"
    commit id: "M03 (M00)"
    commit id: "M0102 (M03)"
```

Internally, history rewriting is a forced rebase on any previous commit of the same branch.