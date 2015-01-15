# Squash and Publish (for Mercurial)

[![Build Status](https://travis-ci.org/mapio/hg-sap.png?branch=master)](https://travis-ci.org/mapio/hg-sap)

This tool can help publishing a Mercurial repositoty squasing intermedaite
(working) commits.

It allows a workflow with a "private" *working* repository (where you perform
frequent commits) that, from time to time, you want to push to a *public*
repository (without keeping track of the intermediate changes).

It works by using the *collapse* capability of the *rebase* extension and
keeping track of the last squashed revision using a *bookmark* (in the working
repository). At every every invocation, it will pull the working repository to
a squasing one where then it will rebase and collapse all the revisions
between the last squashed and the tip, it then pushes the branch with
collapsed revisions to the public repository.

The tool is at a very **alpha stage** and will not check that all the
performed passes complete succesfully; on the other hand, it will never modify
the working repository (except for updating the bookmark), so it should be
"reasonably safe" to be used.
