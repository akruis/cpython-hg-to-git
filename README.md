Task
====

* Evaluate various hg to git repository conversion options and find out the optimal way to convert Cpython hg repository
  at http://hg.python.org to a git repository suitable to be hosted at github.

Constraints
===========

* [PEP-0512](https://www.python.org/dev/peps/pep-0512) defines the various characteristics of the desirable outcome.
* We will want to use an external, well tested tool used by other projects for similar conversion.


Tools
=====

* fast-export

Nicolás Alvarez shared that The fast-export tool started at about 500 revs/sec but progressively slowed down [Ref](https://mail.python.org/pipermail/core-workflow/2016-February/000468.html)

* git-remote-hg transport

Oleg Broytman tried this and shared git-remote-hg provides bidirectional transport. You can continue pulling from
Mercurial repository(ies) and you can commit and push back to Mercurial repository(ies).


* https://import.github.com/

This tool was introduced by Github. I evaluated this tool to do the migration of http://hg.python.org/cpython repo to gihub.
Unfortunately, it failed at 78% migration. Multiple attempts did not help.  I have raised a ticket with github.com citing unsuccessful migration using their tool.

```
*Github Response*

(GitHub Staff)

Hi Senthil,

Unfortunately, the repository is too large to migrate using the importer. I'd recommend converting it to git locally using something like hg-fast-export. Due to its size, you'll need to push the local repo to GitHub in chunks.

Thanks for contacting us and have a great week!
```

* [hg-git](http://hg-git.github.io/)

Pierre-Yves David shared that hg-git was used in real world on large sized repositories and works perfectly fine. This led me research this option further.

The export takes around 4 hours

```
20:31 $ hg push /Users/senthilkumaran/python/trials/git/cpython/
pushing to /Users/senthilkumaran/python/trials/git/cpython/
exporting [=========================================================================================================>                                         searching for changes
adding objects
added 100300 commits with 314150 trees and 202085 blobs
✔ ~/python/trials/cpython
03:05 $
```

Migrating using hg-git was successful. Some care was required while navigating this territory and for the initial setup.

  * Hit https://bitbucket.org/durin42/hg-git/issues/93/pushing-to-empty-repository-on-github
    * hg-git migration on empty / initialized repository failed.
    * hg-git migration on local bare git repository was successful
  
  * After migrating to bare git repository, I had to do piecemeal pushes of the initial git repository to github.
  
* Pushing the completely repository fails.

```  
$ git push github master
Counting objects: 574454, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (572378/572378), done.
remote: fatal: pack exceeds maximum allowed size
error: pack-objects died of signal 13
error: failed to push some refs to 'git@github.com:orsenthil/cpython-hg-git-test-4.git'
```

* This was resolved by pushing few commits at a time.

```
$ git push github master~55000:refs/heads/master
$ git push github master~45000:refs/heads/master
$ git push github master~35000:refs/heads/master
$ git push github master~25000:refs/heads/master
$ git push github master~10000:refs/heads/master
$ git push github master~5000:refs/heads/master
$ git push github master:master
$ git push --all github
```

Workflow Suggestions
====================

* http://www.catb.org/esr/reposurgeon/




