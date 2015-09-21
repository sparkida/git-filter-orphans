
Git-filter-orphans
==================

Git-filter-orphans will find all your orphaned objects and
eradicate them from your git history and repo completely.


COMMANDS
--------
  find         -find all orphan objects and store them in a list($SORT_FILE)
  update       -update your new repo and force push to remote
  clone        -make a backup of your current repo and clone from it in: $REPO_ROOT
  gc           -run manual garbage collection on repo (potentially dangerous)
  rebase       -similar to checkout, but used to important rewrites already made to a remote repo
                so you don't need to reclone the entire repo again
  checkout     -checkout all remote branches, !IMPORTANT - this must be done
                before you filter in order to filter all branches properly for push
  purge FILE   -purge an object list, expects a gfo-sorted.txt type of list
    --tmp DIR    -an off-disk temporary directory to optimize the filter process
                  the directory will be created and deleted on completion
  rmdir PATH   -recursively filter all items of a directory[PATH]; you can also pass in a text
                based file similar to gfo-sorted.txt and use it as a list of directories to delete
    --tmp DIR    -same as above
  filter PATH  -filter a single orphaned file or directory, will not recurse a directory
    --tmp DIR    -same as above
  help GIT-USERNAME CLONE-REPO
               -this menu, you can pass a username and clone-repo name into the params
                be used in place of <username> and <clone-repo> below
  Note: might be a good idea to test this on a cloned version of your repo


INSTALL gfo
-----------
```
cd && git clone https://github.com/sparkida/git-filter-orphans
cd /usr/bin && sudo ln -s ~/git-filter-orphans/git-filter-orphans gfo
```

EXAMPLES
--------
  - build list file -> gfo-sorted.txt
  $ $REPO> gfo find

  - confirm contents of the generated list
  $ $REPO> cat gfo-sorted.txt

  - purge the list created in "gfo-sorted.txt":
  $ $REPO> gfo purge gfo-sorted.txt


COMPLETE GUIDE - READ FIRST
---------------------------
This assumes you want to create a clone of your repo for liability
purposes and have already created a new repo for this.

#create the clone
  $ > git clone --bare git@github.com:$GIT_USER/$REPO ${REPO}.git
  $ > cd ${REPO}.git
  $ ${REPO}.git> git push --mirror git@github.com:$GIT_USER/$CLONE_REPO

#clone the new remote repo
  $ ${REPO}.git> cd .. && git clone git@github.com:$GIT_USER/$CLONE_REPO && cd "$CLONE_REPO"

#checkout all the branches, or you can manually checkout branches, just be sure to check back in to master
  $ $CLONE_REPO> gfo checkout

#create a $SORT_FILE file of orphaned objects and their size in bytes
  $ $CLONE_REPO> gfo find

#the $SORT_FILE looks good
#lets delete the items in it with git-filter-branch
  $ $CLONE_REPO> gfo purge $SORT_FILE

#now we have a clean repo ready to be pushed, this runs gc
  $ $CLONE_REPO> gfo update

#!important anyone else using this repo will need to fetch all the updated refs
#but you will not have to do this
  $ $CLONE_REPO> gfo rebase

