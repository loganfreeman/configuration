[How to update a single gem conservatively](http://makandracards.com/makandra/13885-how-to-update-a-single-gem-conservatively)
---
1. This will work if all dependencies for the update are already satisfied.

 - Find out the version you want to update to
 - Change it directly in Gemfile.lock
 - Run bundle install and see if that worked
 
2. This will work if the gem has no shared dependencies with other gems.

 - Find out the version you want to update to.
 - Add that version explicitly to the Gemfile with , '=1.2.3'
 - Run bundle install
 - Remove the explicit version number again
 - Run bundle install once more
 
3. This should always work.

 - Run bundle update GEMNAME
 - Run git diff Gemfile.lock and notice all the updates you didn't want
 - Revert the unwanted changes to Gemfile.lock you don't want (manually or by staging changed lines one-by-one), leaving only the desired updates.
 - Run bundle install and see if that worked
 
