Git Commits
===========

Take into account the general rules of [Git commit messages](https://gist.github.com/1475276)

Do
--

Write the summary line and description of what you have done in the imperative mode, that is as if you were commanding someone. Write “fix”, “add”, “change” instead of “fixed”, “added”, “changed”.

Always leave the second line blank.

Line break the commit message (to make the commit message readable without having to scroll horizontally in gitk).

DON’T
-----

Don’t end the summary line with a period.

Tips
----
If it seems difficult to summarize what your commit does, it may be because it includes several logical changes or bug fixes, and are better split up into several commits using `git add -p`.

To easily find a good sentence for the summary, imagine saying: 'This commit will …'


Delivering features
===================

 1. git checkout develop
 2. git merge feature-branch --no-ff

Set as commit message:

 1. Summary line, make short notice of changes respecting the rules above
 2. Empty line
 3. paste in userstory text from pivotal
 4. Emty line
 5. Link to pivotal user story
 6. Eventual reference to redmine
 7. Add extra notices that are relevant for releasing

Example:

    Add product detail link when user hovers over room

    As a mouse user I want to see a more info button when I hover over the
    room so that I can visit the standard product detail page

    https://www.pivotaltracker.com/story/show/40514273


Release notes overview
======================

To show the feature merges to create release notes:

    git log --merges

To mix the merge and squash commits in one overview, you can search for
terms that are mandatory in a feature merge commit

    git log --grep=pivotal



