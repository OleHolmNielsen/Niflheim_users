File security on Niflheim
=========================

In light of the possibilities to use AI for automated espionage/snooping we need to consider the amount of openness on Niflheim.
Three models of file security can easily be implemented as listed below.

We recommend model 2 **The GROUP model** as a reasonable compromise between security and ease of use.
However, each research section should decide which model is appropriate for them,
and the section leader should take responsibility for the decision.

1. The OPEN model (the current default)
----------------------------------------

All files are readable by everyone, except for a few sensitive folders where the application software automatically locks down files.

**Any user of Niflheim can read your files**,
as part of a collaboration or with more nefarious purposes.
Specific folders can be protected further if desired.

2. The GROUP model
-------------------

**People in the same** Unix_group_ **can read your files**.

Employees including PhD students are members of a Unix_group_ named ``xxxvip``.
M.Sc. and B.Sc. students are member of the corresponding ``xxxstud`` Unix_group_, and supervisors (incl Ph.D. students???) are secondary members of that Unix_group_ too.
Within this model, faculty, postdocs and Ph.D students can read each others' files,
and they can read the M.Sc./B.Sc. students' files,
but M.Sc./B.Sc. students cannot read the files of the researchers,
unless specific folders are opened up.

.. _Unix_group: https://docs.oracle.com/cd/E19120-01/open.solaris/819-2379/userconcept-35906/index.html

3. The PARANOID model
----------------------

**Noone else can read your files**.

Special access in the GROUP and PARANOID models
-----------------------------------------------

It is possible to open folders for access by either your Unix_group_ or everyone.

Discussion point
----------------

What should be the default security model at Niflheim?
Individual users can override this if they so desire.

.. warning::

   Encrypt your files with real secrets! If somebody gains root access to a desktop that can mount Niflheim folders, they can access your files!

.. raw:: pdf

   PageBreak

Technical stuff
==================

We recommend contacting **Niflheim Support** at <FYS-mail-Niflheim-support@dtu.dk> to set stuff up as you desire.
Below is the do-it-yourself description.

Implementing the OPEN, GROUP and PARANOID models without exceptions
-------------------------------------------------------------------

This is done by setting the appropriate permissions on your home folder.
If you have no open folders, locking down your home folder is sufficient.
If you want to open specific subfolders you have to do a bit more, see below.

You set the appropriate permission of your home folder like this:

.. code-block:: bash

   chmod og=rx $HOME     # Open model, permission -rwxr-xr-x
   chmod g=rx,o= $HOME   # Group model, permission -rwxr-x---
   chmod og= $HOME       # Paranoid model, permission -rwx------

Note: the ``g`` and/or ``o`` in the permission string sets up permissions for your group and for other users.

Use the ls_command_ to view your folders:

.. code-block:: bash

   ls -l $HOME

.. _ls_command: https://en.wikipedia.org/wiki/Ls

Closing specific folders in the OPEN or GROUP models
----------------------------------------------------

You can always close off a folder so it is either private-only, or only accessible to your group.
You do that by:

.. code-block:: bash

   chmod g=rx,o= folder  # Only the group can read/list the folder
   chmod og= folder      # Nobody can access the folder

Giving students access in the GROUP model
-----------------------------------------

If you are in group ``xxxvip`` you may want to make a folder readable for ``xxxstud``. First you need to give execute access but no read access to your home folder, then people can access a file or folder *if they know its name AND have permissions to the file/folder*, but they cannot list your file names. Then you lock down all subfolders and files in your home folder.  Finally, you can open the specific folder.

.. warning::

   This recipe only partly locks your home folder, as people still have execute access to it.  This means that while people cannot run ``ls`` on your home folder, they can read some metadata about files and folders *if they can guess the file names* (you can ``stat`` the file).  An AI can make very qualified guesses about file names!  But only top-level folder names and file names can be verified in this way.

   PLEASE NOTE: If that is not acceptable, the only solution is to have the shared folder on a separate file system from your home folder.

Make your home folder **readable but not listable** for people outside ``xxxvip``:

.. code-block:: bash

   chmod o=x $HOME

Lock down all files and folders in your home folder:

.. code-block:: bash

   chmod o= $HOME/* $HOME/.??*

Make sure that any new files/folders created are also locked down by adding this line to your ``.bashrc`` file:

.. code-block:: bash

   umask 0027   # Alternative syntax: umask u=rwx,g=rx,o=

Now you need to open your folder (here named ``shared``) with the desired permissions.

- To make the folder readable by **members of the group ``xxxstud``** it must be assigned to the group
  and the [setgid bit](https://en.wikipedia.org/wiki/Setuid) must be set so files inside are also assigned to the group:

  .. code-block:: bash

     chgrp -R xxxstud shared
     chmod g=rxs,o= shared

  Files and subfolder created in ``shared``, or copied there with ``cp``,
  will belong to the ``xxxstud`` Unix_group_,
  but files `moved` there (with ``mv``) need to have their group changed with ``chgrp`` before they become readable by ``xxxstud``.

- To make a folder, subfolders or files **readable by everyone**,
  the folder and all subfolders should have read and execute permissions for everyone,
  and files should have read permission for everyone:

  .. code-block:: bash

     chmod og+rx folder
     find folder -type f -exec chmod og+r {} +
     find folder -type d -exec chmod og+rx {} +

