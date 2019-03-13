---
author: peter
categories:
- technology
date: 2015-08-19T20:52:16Z
dsq_thread_id:
- "5601003880"
guid: http://blogs.merikan.com/peter/?p=573
id: 573
title: Move a git repository to another location.
url: /2015/08/19/move-a-git-repository-to-another-location/
tags:
 - git
---

<div>
  <p>
    You might think that when moving a git repository from one location to another, all you have to do is to clone and then push to the new location. That is not true. You have to make sure that all remote branches and tags are included. Note that the following instructions will also push all your local branches to.
  </p>
</div>

<div>
  <p>
    The following steps will move a repository called <em>my_repo</em> from Google Code to Github. Of course you can use the same steps to move from and to which ever git hosting service you like.
  </p>
</div>

<div>
  <strong>Step by step instructions</strong>
</div>

<div>
</div>

  1. Clone the the repository <pre class="brush: bash; title: ; notranslate" title="">git clone https://code.google.com/p/my_repo
cd my_repo
</pre>

  2. List all local and remote branches. **Note:** All branches will be pushed later, both local and remote. <pre class="brush: bash; title: ; notranslate" title="">git branch -a
</pre>

  3. Track all remote branches. <pre class="brush: bash; title: ; notranslate" title="">for remote in `git branch -r | grep -v '\-&gt;'`; do git branch --track ${remote#*/} $remote; done
</pre>

  4. Add the new remote repository. <pre class="brush: bash; title: ; notranslate" title="">git remote add new-origin git@github.com:user/my_repo.git
</pre>

  5. <div>
      <p>
        List all remote repositories.
      </p>
      
      <pre class="brush: bash; title: ; notranslate" title="">
git remote -v
</pre>
    </div>

  6. Push all branches to the new remote repository location. <pre class="brush: bash; title: ; notranslate" title="">git push -u --all new-origin
</pre>

  7. Push all tags to the new remote repository location. <pre class="brush: bash; title: ; notranslate" title="">git push --tags new-origin
</pre>

  8. Remove or rename the old origin <pre class="brush: bash; title: ; notranslate" title="">git remote rm origin  # or rename ‘git remote rename origin old-origin'
</pre>

  9. Rename the new-origin. <pre class="brush: bash; title: ; notranslate" title="">git remote rename new-origin origin
</pre>

 10. <div>
      <div>
        <p>
          List all remote repositories.
        </p>
        
        <pre class="brush: bash; title: ; notranslate" title="">
git remote -v
</pre>
      </div>
    </div>

<div>
  Thats it!
</div>