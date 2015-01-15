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

## Warning

The tool is at a very **alpha stage** and will not check that all the
performed passes complete succesfully; on the other hand, it will never modify
the working repository (except for updating the bookmark), so it should be
"reasonably safe" to be used.
