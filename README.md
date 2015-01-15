# Squash and Publish (for Mercurial)


[![Build Status](https://travis-ci.org/mapio/hg-sap.png?branch=master)](https://travis-ci.org/mapio/hg-sap)

This tool can help to publish a Mercurial repositoty squasing intermedaite
(working) commits.

It allows a workflow with a "private" *working* repository (where you perform
frequent commits) that, from time to time, you want to push to a *public*
repository (without keeping track of the intermediate changes).

It works using the
[collapse](http://mercurial.selenic.com/wiki/RebaseExtension#Collapsing)
capability of the
[rebase](http://mercurial.selenic.com/wiki/RebaseExtension)
extension and
keeping track of the last squashed revision using a
[bookmark](http://mercurial.selenic.com/wiki/Bookmarks)
(in the working repository). At every every invocation, it will pull the
working repository to a squasing one where then it will rebase and collapse
all the revisions between the last squashed and the tip, it then pushes the
branch with collapsed revisions to the public repository.


## Example

As an example, consider the following execution:

	$ hg init work

	$ cd work
	... do some work ...
	$ hg commit
	... do some work ...
	$ hg commit
	... do some work ...
	$ hg commit
	... do some work ...
	$ hg commit

	$ cd ..
	$ hg-sap work public
	*** Squasing 'work' revs 4417615f6ecb:6b4fa3685130 in 'public'...   done!
	*** Revs in 'work':
	6b4fa3685130 Version 3
	29ccfc46fbfa Version 2
	4417615f6ecb Version 1
	f258fed5de13 Version 0
	*** Revs in 'public':
	a83aa5aaacab Squashed from 4417615f6ecb to 6b4fa3685130
	f258fed5de13 Version 0

	$ cd work
	... do some work ...
	$ hg commit

	$ cd ..
	$ hg-sap work public
	*** Squasing 'work' revs 8d89cd5a6f4b:8d89cd5a6f4b in 'public'...   done!
	*** Revs in 'work':
	8d89cd5a6f4b Version 4
	6b4fa3685130 Version 3
	29ccfc46fbfa Version 2
	4417615f6ecb Version 1
	f258fed5de13 Version 0
	*** Revs in 'public':
	b967a0910e60 Squashed from 8d89cd5a6f4b to 8d89cd5a6f4b
	a83aa5aaacab Squashed from 4417615f6ecb to 6b4fa3685130
	f258fed5de13 Version 0

## Exercising Mercurial

As a side tool, developed in order to test `hg-sap`, in `test/functions` you
can find a set of shell functions to exercise Mercurial in a randomized way.

That is to say, you have a function to:

- `add` a file (with random name and contaning random text),
- `remove` a file (at random),
- `edit` a file (adding, or deleting lines at random, containing random text),
- `rename` a file (choosen at random, in another with a random name),
- `work` that performs a random numer of the previous actions (choosen at random).

All functions operate on a repository named after the `$REPOSITORY`
environment variable. For example, you can run it as:

	$ export REPOSITORY=a_test_repo
	$ . ./test/functions
	$ add
	add file 'file-L9B.txt'
	$ add
	add file 'file-JUI.txt'
	$ edit
	edit file 'file-JUI.txt'
	$ edit
	edit file 'file-JUI.txt'
	$ edit
	edit file 'file-L9B.txt'
	$ delete
	delete file 'file-L9B.txt'
	$ rename
	rename file 'file-JUI.txt' to 'file-YGC.txt'
	$ hg -R $REPOSITORY log -r 0:tip --template '{rev} {desc}\n'
	0 add file 'file-L9B.txt'
	1 add file 'file-JUI.txt'
	2 edit file 'file-JUI.txt'
	3 edit file 'file-JUI.txt'
	4 edit file 'file-L9B.txt'
	5 delete file 'file-L9B.txt'
	6 rename file 'file-JUI.txt' to 'file-YGC.txt'

Such file is used in the `test/randomized` test.

## Warning

The tool is at a very **alpha stage** and will not check that all the
performed passes complete succesfully; on the other hand, it will never modify
the working repository (except for updating the bookmark), so it should be
"reasonably safe" to be used.


![Analytics](https://ga-beacon.appspot.com/UA-377250-20/hg-sap?pixel)
