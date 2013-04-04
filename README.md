Git Issue
=========

An issue tracker for Git built around Git in Python.

Git issue will use your local repository to store the issues. This allows
you to address issues offline and then on push sync issues with others.
Issues abuse dot-files, so a `.git-issues` will be created. 

Multiple hooks are created when you run git issue.


Quick Run Down
==============

    git issue create
    git issue list
    git issue resolve
    git issue delete


Requirements
============

- At least Python 2.7
- `sh`


Usage
=====

Creating an Issue
-----------------

Invokes `$EDITOR` and creates a new issue.

    git issue create
    >> created issue 1 on branch master at commit 2fd4e1c6.

The structure of creating `git issue` needs to follow one of the following
file conventions:

Full Structure
--------------

The full structure includes a summary, milestones and/or tags and/or 
assignment, and finally a detailed explanation of the issue.


    Capitalized, short (50 chars or less) summary

    Milestone: milestone1 milestone2

    Tag: tag1 tag2 tag3

    Assign: Justin Bruce Van Horne <justinvh@gmail.com>

    More detailed explanatory text, if necessary.  Wrap it to about 72
    characters or so.  In some contexts, the first line is treated as the
    subject of an email and the rest of the text as the body.  The blank
    line separating the summary from the body is critical (unless you omit
    the body entirely);


Small Structure
--------------

The small structure includes a summary with milestones and/or tags and/or 
assignment of the issue.


    Capitalized, short (50 chars or less) summary

    Milestone: milestone1 milestone2

    Tags: tag1 tag2 tag3

    Assign: Justin Bruce Van Horne <justinvh@gmail.com>


Minimal Structure
--------------

The minimal structure is just a summary.

    Capitalized, short (50 chars or less) summary


If you don't follow this structure, then your issue will be rejected. If you 
don't like this structure, then it is best to fork the project and deal with
it rather than send PULL requests to fit arbitrary formats.


Assigning an Issue
--------------------------------

You can assign an issue to an individual by specifying either their git
user and email or alternatively just their email. Either way an email needs
to be specified. This action is always retroactive edit.

    git issue --amend 1 --assign "Justin Bruce Van Horne <justinvh@gmail.com>"
    >> Emailing issue #1 to Justin Bruce Van Horne at justinvh@gmail.com

Multiple users can be specified at the command line as well.

    git issue --amend 1 --assign "Justin Bruce Van Horne <justinvh@gmail.com>"\
                       --assign "Anonymous Person <anonymous@gmail.com>"\
                       --assign "anonymous2@gmail.com"
    >> Emailing issue #1 to Justin Bruce Van Horne at justinvh@gmail.com
    >> Emailing issue #1 to Anonymous Person <anonymous@gmail.com>
    >> Emailing issue #1 to anonymous2@gmail.com


Editing an Issue
----------------

You can edit issues from the command-line or just plainly edit it all-together.
This will re-prompt you with your `$EDITOR`

    git issue --amend 1

You can modify other attributes such as the title, tags, milestones,
assignments, and description.

    git issue --amend 1 --title "A new title"
    >> <<Outputs the entire issue again>>

    git issue --amend 1 --tag foo --tags bar
    >> <<Outputs the entire issue again>>

    git issue --amend 1 --milestone milestone1
    >> <<Outputs the entire issue again>>

    git issue --amend 1 --description "A description of some sort"
    >> <<Outputs the entire issue again>>


Resolving an Issue Automatically
---------------------------------

By default you can mark an issue resolved by simply running:

    git issue resolve

`resolve` will look at the git log history for the last commit that follows
a particular pattern implying a resolved issue. For example, if your last
commit messaged looked like:

    Switch libvirt get_cpu_info method over to use config APIs

    The get_cpu_info method in the libvirt driver currently uses
    XPath queries to extract information from the capabilities
    XML document. Switch this over to use the new config class
    LibvirtConfigCaps. Also provide a test case to validate
    the data being returned

    Fixes: issue #1

Then this message would mark issue #1 resolved. You can resolve multiple
issues in a single commit as well.

    Fixes: issue #1, issue #2, issue #3

If an issue can not be resolved from a commit, then `git issue` will let you
know and verify your action. For example: we create an issue #1, but not
issue #2, then the following would happen:

    git issue resolve
    >> Failed to resolve issue #2 on branch master at commit 2fd4e1c6.
    >> Do you want to continue (y/n): y
    >> Creating tag issue-1-resolved at 2fd4e1c6.

Alternatively if no issues can be found, then the following happens:

    git issue resolve
    >> No issues can be found.


Resolving an Issue Manually
---------------------------

You can resolve an issue by just specifying the issue number and the commit.

    git issue resolve --id 1 --commit 2fd4e1c6
    >> Creating tag issue-1-resolved at 2fd4e1c6.

You can specify multiple `--id` with a single `--commit`.

Looking at Issues
-----------------

    # lists all unresolved issues
    git issue list
    >> Issue #1 - A short description of issue #1 {milestone} [tag] [foo]
    >> Issue #2 - A short description of issue #2 {milestone} {milestone} [bar]
    >> Issue #3 - A short description of issue #3


    # lists all unresolved issues with the tag "foo"
    git issue list --tag foo
    >> Issue #1 - A short description of issue #1 {milestone} [tag] [foo]


    # lists all unresolved issues with the tag "bar" and milestone "milestone"
    git issue list --tag bar --milestone "milestone"
    >> Issue #2 - A short description of issue #2 {milestone} {milestone} [bar]


    # lists all unresolved issues
    git issue list --unresolved
    >> Issue #1 - A short description of issue #1 {milestone} [tag] [foo]
    >> Issue #2 - A short description of issue #2 {milestone} {milestone} [bar]
    >> Issue #3 - A short description of issue #3


    # lists all resolved issues
    git issue list resolved
    >> Issue #4 - A short description of issue #4 {milestone} [tag] [foo]


    # lists all issues assigned to a certain user
    git issue list --assign "justinvh@gmail.com"
    >> Issue #1 - A short description of issue #1 {milestone} [tag] [foo]
    >> Issue #2 - A short description of issue #2 {milestone} {milestone} [bar]
    >> Issue #3 - A short description of issue #3


Deleting an issue
-----------------

    git issue delete --id 1
    >> Deleting issue:
    >>
    >> Issue #1 - A short description of issue #1 {milestone} [tag] [foo]
    >> Longer description of issue #1
    >> 
    >> Do you want to continue (y/n): y
    >> Issue is deleted.
