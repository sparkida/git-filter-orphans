Git-filter-orphans will find all your orphaned objects and
eradicate them from your git history and repo completely.


COMMANDS
--------
  find         -find all orphan objects and store them in a list(${SORT_FILE})
  update       -update your new repo and force push to remote
  clone        -make a backup of your current repo and clone from it in: ${REPO_ROOT}
  gc           -run manual garbage collection on repo (potentially dangerous)
  filter PATH  -filter a single orphaned file or directory
    --tmp DIR    -same as below
  purge FILE   -purge an object list, expects a gfo-sorted.txt type of list
    --tmp DIR    -an off-disk temporary directory to optimize the filter process
                  the directory will be created and deleted on completion
  help GIT-USERNAME CLONE-REPO
               -this menu, you can pass a username and clone-repo name into the params
                be used in place of <username> and <clone-repo> below
  Note: might be a good idea to test this on a cloned version of your repo



EXAMPLES
--------
  - build list file -> gfo-sorted.txt
  $ ${REPO}> ~/git-filter-orphans find

  - confirm contents of the generated list
  $ ${REPO}> cat gfo-sorted.txt

  - purge the list created in "gfo-sorted.txt":
  $ ${REPO}> ~/git-filter-orphans purge gfo-sorted.txt


COMPLETE GUIDE - READ FIRST
---------------------------
This assumes you want to create a clone of your repo for liability
purposes and have already created a new repo for this.

#create the clone
  $ > git clone --bare git@github.com:${GIT_USER}/${REPO} ${REPO}.git
  $ > cd ${REPO}.git
  $ ${REPO}.git> git push --mirror git@github.com:${GIT_USER}/${CLONE_REPO}

#clone the new remote repo
  $ ${REPO}.git> cd .. && git clone git@github.com:${GIT_USER}/${CLONE_REPO} && cd "${CLONE_REPO}"

#create a ${SORT_FILE} file of orphaned objects and their size in bytes
  $ ${CLONE_REPO}> ~/git-filter-orphans find

#the ${SORT_FILE} looks good
#lets delete the items in it with git-filter-branch
  $ ${CLONE_REPO}> ~/git-filter-orphans purge ${SORT_FILE}

#for this next step we need to clean our old references
#the easiest way is to just clone the repo locally and copy your backup git config
  $ ${CLONE_REPO}> ~/git-filter-orphan clone
	
#or run the manual GC actions needed to clear the repo of it's history, but be careful,
#the git commands ran here can be volatile, might want to make a backup
#see: http://git-scm.com/docs/git-filter-branch#_checklist_for_shrinking_a_repository
  $ ${CLONE_REPO}> ~/git-filter-orphans gc

#now we have a clean repo ready to be pushed
  $ ${CLONE_REPO}> ~/git-filter-orphans update
