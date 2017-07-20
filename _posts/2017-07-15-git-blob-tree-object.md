---
title: GIT - relationship between git add, git commit and GIT internal representation
layout: default
---

{{ page.title }}
================
<head>
<style>
pre {
    display: block;
    font-family: monospace;
    white-space: pre;
    margin: 1em 0;
}
</style>
</head>

GIT differs from CVS or SVN in how it store/manage sources. The latter store differences between commits while the former stores a snapshot of each commit (blob object and tree object).

Working Directory - is the directory in which files for GIT repository resides. It contains a .git subdirectory. One would either 
use git init or git clone to have a working directory.

Files in a working directory could be in one of the following state:

* Unstaged - GIT do not track these files for instance a newly created file.
* Staged - GIT track the files state. GIT knows whether the file was newly added to the repositiory or it has been modified. 
* Committed - files that has been stored in GIT repository.


Let's walk through a sample flow, in which a new GIT repository is created and a new file is added to GIT repository.

1. A *Working Directory* is created using **git init**.
	<pre>
	git init git-sandbox
	Initialized empty Git repository in /home/brian/git-sandbox/.git/
	~/git-sandbox$ ls -al
	total 12
	drwxrwxr-x  3 brian brian 4096 Jul 16 19:57 .
	drwxr-xr-x 10 brian brian 4096 Jul 16 19:57 ..
	drwxrwxr-x  7 brian brian 4096 Jul 16 19:57 .git
	</pre>
2. Add a file into the *Working Directory*. 
	<pre>
	~/git-sandbox$ echo 'some random text' > file-1.txt
	</pre>
3. The newly created file is *Unstaged*. Check the state of the newly created file using git status.
	<pre>
	~/git-sandbox$ git status
	# On branch master
	#
	# Initial commit
	#
	# Untracked files:
	#   (use "git add <file>..." to include in what will be committed)
	#
	#	file-1.txt
	</pre>
4. Add the newly created file into GIT repository. 
	<pre>
	~/git-sandbox$ git add file-1.txt
	</pre>
5. Now, the file state changed from *Unstaged* to *Staged*.
	<pre>
	~/git-sandbox$ git status
	# On branch master
	#
	# Initial commit
	#
	# Changes to be committed:
	#   (use "git rm --cached <file>..." to unstage)
	#
	#	new file:   file-1.txt
	#
	</pre>
6. Commit the file.
	<pre>
	~/git-sandbox$ git commit file-1.txt -m "initial commit for file-1"
	[master (root-commit) f136f9d] initial commit for file-1
	 1 file changed, 1 insertion(+)
	 create mode 100644 file-1.txt
	</pre>
7. Check the file has been committed by looking at the git log.
	<pre>
	~/git-sandbox$ git log --oneline --color --graph --all --decorate
	* f136f9d (HEAD -> master) initial commit for file-1
	</pre>



### When git command 'add' is issued, under the hood, GIT creates a blob object and update the index file. 

**GIT Blob Object**

A blob object is a file containing the content of the newly added file and its header. The blob object file name is an SHA-1 hash which is a checksum of the content of the newly added file and a header. The blob object is stored in .git/objects directory. You can see the newly added blob as below:
	<pre>
	~/git-sandbox$ find ./.git/objects -type f
	./.git/objects/4a/b08b37d5bcb2a091cffba62102e4e875ae82b8
	~/git-sandbox$ git cat-file -t 4ab08b37d5bcb2a091cffba62102e4e875ae82b8
	blob
	~/git-sandbox$ git cat-file -p 4ab08b37d5bcb2a091cffba62102e4e875ae82b8
	some random text
	</pre>

**GIT Index file**

Git Index file list files that has been added to git to be tracked. It is files in staging state and are the set of files to be commited on the next commit command. To see the index file use the git-ls-files command.
	<pre>
	~/git-sandbox$ git ls-files -s
	100644 4ab08b37d5bcb2a091cffba62102e4e875ae82b8 0	file-1.txt
	</pre>

Output above show's the object's mode bits, stage SHA-1 number and the object name.

### When git command 'commit' is issued, under the hood, GIT creates a tree object and a commit object.

**GIT Tree Object**

An object to group references to blob object or/and other tree object. Each references is a SHA-1 pointer. Taking above example, the newly created tree object has one SHA-1 reference of a blob object.
	<pre>
	~/git-sandbox$ git cat-file -t 7494fdaa460caa27ea60565a85a637bba27a7ddb
	tree
	~/git-sandbox$ git cat-file -p 7494fdaa460caa27ea60565a85a637bba27a7ddb
	100644 blob 4ab08b37d5bcb2a091cffba62102e4e875ae82b8	file-1.txt
	</pre>

**GIT Commit Object**

An object to contain information of the commit i.e. the tree object, author, commit comment and commit time.
	<pre>
	~/git-sandbox$ git cat-file -t f136f9d1b01b03717a6388f449e21fa9bac999f5
	commit
	~/git-sandbox$ git cat-file -p f136f9d1b01b03717a6388f449e21fa9bac999f5
	tree 7494fdaa460caa27ea60565a85a637bba27a7ddb
	author Brian <brian@brian.com> 1500382747 +0800
	committer Brian <brian@brian.com> 1500382747 +0800
	
	initial commit for file-1
	</pre>

[Ref 1 - https://git-scm.com/book/en/v2/Git-Internals-Git-Objects](https://git-scm.com/book/en/v2/Git-Internals-Git-Objects)
[Ref 2 - http://shafiulazam.com/gitbook/1_the_git_object_model.html](http://shafiulazam.com/gitbook/1_the_git_object_model.html)
