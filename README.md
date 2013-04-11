Git Issue
=========

An issue tracker for Git built around Git in Python.

Git issue will use your local repository to store the issues. This allows
you to address issues offline and then on push sync issues with others.

Multiple hooks are created when you run git issue.


Quick Run Down
==============

    git issue create
    git issue list
    git issue resolve
    git issue delete


Try it out
==========

`git-issue` uses itself for issue tracking. You can give it a whirl
by simply:

    $ git clone git://github.com/justinvh/git-issue.git
    $ cd git-issue
    $ env PATH=$PWD:$PATH git issue list

`git-issues` will automatically checkout the remote git-issues branch
and keep it up to date by doing a pull on any command. If you feel that
this is too noisy you can change: `git config issue.autopull no` or
just pass `--no-pull` before the action: `git issue --no-pull list`.


On First Run
------------
On your first run of git issue -- that is any action -- it will search across
your remotes for a git-issues branch. If it exists, then it will checkout
that remote and sync your issues. On successive calls it will always make
sure your git-issues branch is up-to-date.

If the remote does not exist, then it will create a branch called git-issues.
Issues created are very straight-forward. 


Requirements
============

- At least Python 2.7


Customization
=============

Most of `git-issue` can be customized to fit your needs. This includes:

- Changing text to fit your respective language
- Modifying default editor
- Adjusting the template text

By default `git-issue` will try to look up parameters from your `git-config`
environment. This means you can overload parameters on a per-project basis.

`git-issue` was designed such that most of the user-configurable options
are at the beginning of the file. It should be very straight-forward to
change parameters with minimal knowledge of Python.

Default Parameters
-------------------

You can adjust any of these parameters by using `git config parameter value`:


| Parameter                | Default    |
| ------------------------ | ---------- |
| issue.branch             | git-issues |
| issue.autopull           | yes        |
| issue.editor.environment | EDITOR     |
| issue.editor.path        | vim        |
| issue.tokens.assign      | assign:    |
| issue.tokens.tag         | tags:      |
| issue.tokens.milestone   | milestone: |
| issue.tokens.fixes       | fixes:     |
| issue.tokens.type        | type:      |
| issue.defaults.type      | bug        |


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

    git issue --amend 03afad --assign "Justin Van Horne <justinvh@gmail.com>"
    >> Emailing issue 03afad to Justin Van Horne at justinvh@gmail.com

Multiple users can be specified at the command line as well.

    git issue --amend 03afad --assign "Justin Van Horne <justinvh@gmail.com>"\
                             --assign "A. Person <anonymous@gmail.com>"\
                             --assign "anonymous@gmail.com"
    >> Emailing issue 03afad to Justin Van Horne at justinvh@gmail.com
    >> Emailing issue 03afad to A. Person <anonymous@gmail.com>
    >> Emailing issue 03afad to anonymous2@gmail.com


Editing an Issue
----------------

You can edit issues from the command-line or just plainly edit it all-together.
This will re-prompt you with your `$EDITOR`

    git issue --amend 0xdead

You can modify other attributes such as the title, tags, milestones,
assignments, and description.

    git issue --amend 0xdead --title "A new title"
    >> <<Outputs the entire issue again>>

    git issue --amend 0xdead --tag foo --tags bar
    >> <<Outputs the entire issue again>>

    git issue --amend 0xdead --milestone milestone1
    >> <<Outputs the entire issue again>>

    git issue --amend 0xdead --description "A description of some sort"
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

    Fixes: 03afad

Then this message would mark issue 03afad resolved. You can resolve multiple
issues in a single commit as well.

    Fixes: 03afad, 069djd, 8ujnfd

If an issue can not be resolved from a commit, then `git issue` will let you
know and verify your action. For example: we create an issue 03afad, but not
issue 069djd, then the following would happen:

    git issue resolve
    >> Failed to resolve issue 069djd on branch master at commit 2fd4e1c6.
    >> Do you want to continue (y/n): y
    >> Creating tag issue-069djd-resolved at 2fd4e1c6.

Alternatively if no issues can be found, then the following happens:

    git issue resolve
    >> No issues can be found.


Resolving an Issue Manually
---------------------------

You can resolve an issue by just specifying the issue number and the commit.

    git issue resolve --id 0xdead --commit 2fd4e1c6
    >> Creating tag issue-1-resolved at 2fd4e1c6.

You can specify multiple `--id` with a single `--commit`.

Looking at Issues
-----------------

lists all unresolved issues

    $ git issue list

    Issue 03fada unresolved on master/85d65. Created on 1365278151.
    This is a test description under 80 chars
    --------------------------------------------------------------------------
        milestones             tags              assigned
    --------------------------------------------------------------------------
          release            problems        Justin Bruce Va...
      release-holders            -           Bruce Van Horne...
             -                   -           Justin Van Horn...
    --------------------------------------------------------------------------


lists all unresolved issues with the tag "foo"

    $ git issue list --tag foo

    Issue 8jfh3h unresolved on master/85d65. Created on 1365278151.
    This is a test description under 80 chars
    --------------------------------------------------------------------------
        milestones             tags              assigned
    --------------------------------------------------------------------------
          release            problems        Justin Bruce Va...
      release-holders            -           Bruce Van Horne...
             -                   -           Justin Van Horn...
    --------------------------------------------------------------------------


lists all unresolved issues with the tag "bar" and milestone "milestone"

    $ git issue list --tag bar --milestone "milestone"

    Issue 0dhhdu unresolved on master/85d65. Created on 1365278151.
    This is a test description under 80 chars
    --------------------------------------------------------------------------
        milestones             tags              assigned
    --------------------------------------------------------------------------
          release            problems        Justin Bruce Va...
      release-holders            -           Bruce Van Horne...
             -                   -           Justin Van Horn...
    --------------------------------------------------------------------------


lists all unresolved issues

    $ git issue list --unresolved

    Issue 8rufhh unresolved on master/85d65. Created on 1365278151.
    This is a test description under 80 chars
    --------------------------------------------------------------------------
        milestones             tags              assigned
    --------------------------------------------------------------------------
          release            problems        Justin Bruce Va...
      release-holders            -           Bruce Van Horne...
             -                   -           Justin Van Horn...
    --------------------------------------------------------------------------

    Issue 0dsf8d unresolved on git-issues/ab2fe. Created on 1365278414.
    This is a test description under 80 chars, too
    --------------------------------------------------------------------------
        milestones             tags              assigned
    --------------------------------------------------------------------------
             -                   -                   -

    Issue 0dsfj3 unresolved on master/85d65. Created on 1365389962.
    This is a test issue again. Something something.  
    --------------------------------------------------------------------------
        milestones             tags              assigned
    --------------------------------------------------------------------------
          release              test          justinvh@gmail....
    --------------------------------------------------------------------------


lists all resolved issues

    $ git issue list resolved

    Issue e9ijfd unresolved on master/85d65. Created on 1365389962.
    This is a test issue again. Something something.  
    --------------------------------------------------------------------------
        milestones             tags              assigned
    --------------------------------------------------------------------------
          release              test          justinvh@gmail....
    --------------------------------------------------------------------------


lists all issues assigned to a certain user

    $ git issue list --assign "justinvh@gmail.com"

    Issue 8fuifj unresolved on master/85d65. Created on 1365389962.
    This is a test issue again. Something something.  
    --------------------------------------------------------------------------
        milestones             tags              assigned
    --------------------------------------------------------------------------
          release              test          justinvh@gmail....
    --------------------------------------------------------------------------


Deleting an issue
-----------------

    git issue delete --id 0xdead
    >> Deleting issue:
    >>
    >> Issue 03xxdj - A description of issue 03xxdj {milestone} [tag] [foo]
    >> Longer description of issue 03xxdj
    >> 
    >> Do you want to continue (y/n): y
    >> Issue is deleted.
