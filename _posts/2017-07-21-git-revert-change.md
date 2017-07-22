---
title: GIT - how to revert changes in Git?
layout: default
---

{{ page.title }}
================
<head>
<style>
table, th, td {
    border: 1px solid black;
    border-collapse: collapse;
    margin: 5px 0;
    text-align: left;
    vertical-align: top;
}
th { background-color: #99ccff; }
tr { background-color: #e6f2ff; }
pre {
    background: #eee !important;
    display: block;
    font-family: monospace;
    white-space: pre;
    margin: 1em 0;
}
.changedtextcolor {
    color: green
}
</style>
</head>

* [Definition](#definition)
* [Summary of Git Reset on Working Directory, Index and HEAD](#summary-of-git-reset-on-working-directory-index-and-head)
 * [Git reset with path](#git-reset-with-path)
 * [Git reset without path](#git-reset-without-path)
* [Summary of Git Checkout on Working Directory, Index and HEAD](summary-of-git-checkout-on-working-directory-index-and-head)
* [Useful Git commands to check file states](#useful-git-commands-to-check-file-states)
 * [What is in the index?](#what-is-in-the-index)
 * [What is in the HEAD?](#what-is-in-the-head)
* [Revert Scenario and their associated Git command](revert-scenario-and-their-associated-git-command)
 * [Revert changes that has not been committed to Git repository](revert-changes-that-has-not-been-committed-to-git-repository)
 * [Revert changes that has been staged but not committed](revert-changes-that-has-been-staged-but-not-committed)

## Definition
Pre-requisite to understanding this is reading my [previous page](/2017/07/15/git-blob-tree-object.html) on definition of working directory and index file. In addition, we would also need to understand the HEAD reference or HEAD tree.

**HEAD**<br/>
HEAD is the pointer to the last commit of the current branch reference or as simply explained in [Git Reset Demystified](https://git-scm.com/book/en/v2/Git-Tools-Reset-Demystified) as the snapshot of your last commit. Using example from [previous page](/2017/07/15/git-blob-tree-object.html):

<pre>
~/git-sandbox$  git cat-file -p HEAD
tree 7494fdaa460caa27ea60565a85a637bba27a7ddb
author Brian <brian@brian.com> 1500382747 +0800
committer Brian <brian@brian.com> 1500382747 +0800

initial commit for file-1
~/git-sandbox$  git ls-tree -r HEAD
100644 blob 4ab08b37d5bcb2a091cffba62102e4e875ae82b8	file-1.txt
</pre>

In summary the three important concepts as defined in [Git Reset Demystified](https://git-scm.com/book/en/v2/Git-Tools-Reset-Demystified):
<table border="1">
<tr>
 <th>Tree</th>
 <th>Explaination</th>
</tr>
<tr>
 <td>HEAD</td>
 <td>Pointer to the last commit of the current branch reference, i.e. pointer to the last commit you made.</td>
</tr>
<tr>
 <td>Index</td>
 <td>Proposed next commit snapshot.</td>
</tr>
<tr>
 <td>Working Directory</td>
 <td>Sandbox - your scratch space, used to easily modify file content.</td>
</tr>
</table>

## Summary of Git Reset on Working Directory, Index and HEAD

### Git reset with path
<table border="1">
<tr>
 <th>Git Command</th>
 <th>Working Directory</th>
 <th>Index</th>
 <th>Head</th>
</tr>
<tr>
  <td>File state before any command</td>
  <td>file-1.txt (v3)</td>
  <td>file-1.txt (v3)</td>
  <td>file-1.txt (v3)</td>
</tr>
<tr>
  <td>git reset --soft HEAD~ file-1.txt</td>
  <td colspan="3">Error: Cannot do soft reset with paths.</td>
</tr>
<tr>
  <td rowspan="2">git reset --mixed HEAD~ file-1.txt</td>
  <td>no changes</td>
  <td class="changedtextcolor">changed</td>
  <td>no changes</td>
</tr>
<tr>
  <td>file-1.txt (v3)</td>
  <td class="changedtextcolor">file-1.txt (v2)</td>
  <td>file-1.txt (v3)</td>
</tr>
<tr>
  <td rowspan="2">git reset HEAD~ file-1.txt</td>
  <td>no changes</td>
  <td class="changedtextcolor">changed</td>
  <td>no changes</td>
</tr>
<tr>
  <td>file-1.txt (v3)</td>
  <td class="changedtextcolor">file-1.txt (v2)</td>
  <td>file-1.txt (v3)</td>
</tr>
<tr>
  <td>git reset --hard HEAD~ file-1.txt</td>
  <td colspan="3">Error: Cannot do hard reset with paths.</td>
</tr>
</table>


### Git reset without path
<table border="1">
<tr>
 <th>Git Command</th>
 <th>Working Directory</th>
 <th>Index</th>
 <th>Head</th>
</tr>
<tr>
  <td>state before any command</td>
  <td>file-1.txt (v3)</td>
  <td>file-1.txt (v3)</td>
  <td>file-1.txt (v3)</td>
</tr>
<tr>
  <td rowspan="2">git reset --soft HEAD~</td>
  <td>no changes</td>
  <td>no changes</td>
  <td class="changedtextcolor">changed</td>
</tr>
<tr>
  <td>file-1.txt (v3)</td>
  <td>file-1.txt (v3)</td>
  <td class="changedtextcolor">file-1.txt (v2)</td>
</tr>
<tr>
  <td rowspan="2">git reset --mixed HEAD~</td>
  <td>no changes</td>
  <td class="changedtextcolor">changed</td>
  <td class="changedtextcolor">changed</td>
</tr>
<tr>
  <td>file-1.txt (v3)</td>
  <td class="changedtextcolor">file-1.txt (v2)</td>
  <td class="changedtextcolor">file-1.txt (v2)</td>
</tr>
<tr>
  <td rowspan="2">git reset HEAD~</td>
  <td>no changes</td>
  <td class="changedtextcolor">changed</td>
  <td class="changedtextcolor">changed</td>
</tr>
<tr>
  <td>file-1.txt (v3)</td>
  <td class="changedtextcolor">file-1.txt (v2)</td>
  <td class="changedtextcolor">file-1.txt (v2)</td>
</tr>
<tr>
  <td rowspan="2">git reset --hard HEAD~</td>
  <td class="changedtextcolor">changed</td>
  <td class="changedtextcolor">changed</td>
  <td class="changedtextcolor">changed</td>
</tr>
<tr>
  <td class="changedtextcolor">file-1.txt (v2)</td>
  <td class="changedtextcolor">file-1.txt (v2)</td>
  <td class="changedtextcolor">file-1.txt (v2)</td>
</tr>
</table>
**!!If after issuing the command, it changes files in Working Directory, then you would need to be careful as changed file will be overwritten!** For instance when issuing `git reset --hard HEAD~`.

## Summary of Git Checkout on Working Directory, Index and HEAD
Git Checkout command is quite similar to Git Reset as in it manipulates the three trees i.e. Working Directory, Index and HEAD but with two differences as summarized in [Reset Demystified](https://git-scm.com/blog):
1. If using Git Checkout without path, it is Working Directory safe. This means that it will not allow checkout/switch to different branch if there are any unstaged or uncommitted files in your Working Directory.
2. If using Git Checkout without path, it moves the HEAD to branch last commit snapshot (i.e.`git checkout master`), while `git reset --hard master` will moves the HEAD of the branch to whatever master last commit snapshot.


### Git checkout with path
<table border="1">
<tr>
 <th>Git Command</th>
 <th>Working Directory</th>
 <th>Index</th>
 <th>Head</th>
</tr>
<tr>
  <td>File state before any command</td>
  <td>file-1.txt (v3)</td>
  <td>file-1.txt (v3)</td>
  <td>file-1.txt (v3)</td>
</tr>
<tr>
  <td rowspan="2">git checkout HEAD~ file-1.txt</td>
  <td class="changedtextcolor">changed</td>
  <td class="changedtextcolor">changed</td>
  <td>no changes</td>
</tr>
<tr>
  <td class="changedtextcolor">file-1.txt (v2)</td>
  <td class="changedtextcolor">file-1.txt (v2)</td>
  <td>file-1.txt (v3)</td>
</tr>
</table>

### Git checkout without path
<table border="1">
<tr>
 <th>Git Command</th>
 <th>Working Directory</th>
 <th>Index</th>
 <th>Head</th>
</tr>
<tr>
  <td>File state before any command</td>
  <td>MASTER Branch: file-1.txt (v3)</td>
  <td>MASTER Branch: file-1.txt (v3)</td>
  <td>MASTER Branch: file-1.txt (v3)</td>
</tr>
<tr>
  <td colspan="4">In this example, there is another branch named: development, in which file-1.txt has been modified and commited. It will be refer to as file-1.txt (v4).</td>
</tr>
<tr>
  <td rowspan="2">git checkout development</td>
  <td class="changedtextcolor">changed</td>
  <td class="changedtextcolor">changed</td>
  <td class="changedtextcolor">changed</td>
</tr>
<tr>
  <td class="changedtextcolor">DEVELOPMENT Branch: file-1.txt (v4)</td>
  <td class="changedtextcolor">DEVELOPMENT Branch: file-1.txt (v4)</td>
  <td class="changedtextcolor">DEVELOPMENT Branch: file-1.txt (v4)</td>
</tr>
</table>


## Useful Git commands to check file states
### What is in the index?
**with a file path**
<pre>
~/git-sandbox$ git ls-files -s file-2.txt 
100644 6b1455f016cc4b399494372cc652b674ab59ba38 0	file-2.txt
~/git-sandbox$ git cat-file -p 6b1455f016cc4b399494372cc652b674ab59ba38
second file to test
2nd line
</pre>
**without a file path**
<pre>
~/git-sandbox$ git ls-files -s
100644 405e49f302c844960ec6c2db55a4100dbe079cb5 0	file-1.txt
100644 110615840b0fd74873f8b5ab9e7ab288f7d4a3a1 0	file-2.txt
</pre>

### What is in the HEAD?
<pre>
~/git-sandbox$ cat .git/HEAD
ref: refs/heads/master
~/git-sandbox$ git show-ref
316b18333350bded4b68b3a41f5e699df2b43533 refs/heads/development
3ae41d8301497e1945354fbb86d9bd15d157366d refs/heads/master
~/git-sandbox$ git cat-file -p 3ae41d8301497e1945354fbb86d9bd15d157366d
tree 00ab6e9f8446fce73a0ae3a59858f81189ed8a31
parent 0eebf8734514cb65ed7cecb5c11dfa2476acb221
author Brian <brian@brian.com> 1500641325 +0800
committer Brian <brian@brian.com> 1500641325 +0800

3rd line in file
~/git-sandbox$ git ls-tree -r 00ab6e9f8446fce73a0ae3a59858f81189ed8a31
100644 blob 405e49f302c844960ec6c2db55a4100dbe079cb5	file-1.txt
100644 blob 110615840b0fd74873f8b5ab9e7ab288f7d4a3a1	file-2.txt
</pre>


## Revert Scenario and their associated Git command

When trying to revert the changes, it could be changes that has not been committed to Git repository or changes that has been committed to Git repository or changes that has been push to public Git repository.

### Revert changes that has not been committed to Git repository
<table border="1">
<tr>
 <th>Scenario</th>
 <th>Commands</th>
</tr>
<tr>
  <td rowspan="3">Discard current file changed in working directory</td>
  <td>git checkout -- file-1.txt</td>
</tr>
<tr>
  <td>git checkout {treeish|commit-sha1} {filename}</td>
</tr>
<tr>
  <td>git checkout HEAD file-1.txt</td>
</tr>
<tr>
  <td rowspan="3">Revert file to previous n-commit</td>
  <td>git checkout {treeish|commit-sha1} {filename}</td>
</tr>
<tr>
  <td>git checkout f136f9d file-1.txt</td>
</tr>
<tr>
  <td>git checkout HEAD~1 file-1.txt #move to previous version of file-1.txt</td>
</tr>
<tr>
  <td rowspan="2">Discard all files that has changed in working directory</td>
  <td>git checkout HEAD *.txt</td>
</tr>
<tr>
  <td>git reset --hard HEAD</td>
</tr>
</table>
				
### Revert changes that has been staged but not committed
<table border="1">
<tr>
 <th>Scenario</th>
 <th>Commands</th>
</tr>
<tr>
  <td rowspan="2">Discard staged file but leave file changes in working directory</td>
  <td>git reset HEAD file-1.txt</td>
</tr>
<tr>
  <td>git reset --mixed HEAD file-1.txt</td>
</tr>
<tr>
  <td>Discard staged file and revert file to HEAD</td>
  <td>git checkout HEAD file-1.txt</td>
</tr>
<tr>
  <td>Discard all staged files but leave file changes in working directory</td>
  <td>git reset HEAD</td>
</tr>
<tr>
  <td>Discard all staged files but revert files to HEAD</td>
  <td>git reset --hard HEAD</td>
</tr>
</table>
		 
									
				 
	
			

















