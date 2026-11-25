# git<br/>
use "git init " to initiales a file <br/>
use "git status" to see the status of the the file if something changed without commmit it <br/>
use "git add " to stage the file <br/>
use "git add -p " to stage with sepreting the commits <br/>
use "git commit -m " to commit with message <br/>

use "git log" to see the history <br/>
use "git log --oneline " Show One-Line History : Condenses each commit to the first 7 characters of its SHA-1 hash and its commit message.<br/>
use "git --no-pager log -2 " or "git log -n 2" to see ur last 2 commit history <br/>
use 'git --no-pager log --since="5minutes " ' to see ur commits in the last 5mins <br/>
use 'git log --format=" * %h %ad | %s %d [%an]" --date=short' to Show logs in a personalized format, including the commit hash, date, message, branch information, and author name<br/>
use "git checkout" to check it out and moving inside the working tree add a "^" to back a step back <br/>
use "git tag 'tag' " to tag current vesrion of ur commit if the reffrence is empty and if u want qa spsecf refrence type it<br/> 
use "git tag" to Display a list of all tags present in the repository to verify successful tagging<br/>
use "git restore --staged" to Takes the file out of the Staging Area <br/>
use "git restore " to restore change by copies the clean version from the Staging Area back onto your desk, overwriting your edits <br/>
use " git revert " to preserves history and it moves forward by adding an inverse commit <br/>
use "git reset --hard <target>" to moves main (and HEAD) back to v1 and discards everything after it from the branch. <br/>
use "git log --oneline --all " to shows every ref, including oops, so the "deleted" commits appear <br/>
use "git tag -d"to delete the tag <br/>
use "git reflog expire --expire=now --all" The reflog also remembers old positions of HEAD (by default for ~90 days), so it must be expired.<br/>
use "git gc --prune=now " to physically removes unreachable objects.<br/>

