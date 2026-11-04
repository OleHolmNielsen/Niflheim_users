=========================
File security on Niflheim
=========================

In light of the possibilities to use AI for automated espionage/snooping we need to consider the amount of openness on Niflheim.
Three models of file security can fairly easily be implemented as discussed below.

On Niflheim we recommend model **2. The GROUP model** as a convenient compromise between security and ease of use.
However, each research section should decide which model is appropriate for them in view of the sensitivity of their data,
and the section leader should take responsibility for the decision.

1. The OPEN model (the current default)
----------------------------------------

**Any user of Niflheim can read your files**,
as part of a collaboration, or with more nefarious purposes.
Specific folders can be protected further if desired.

All files are readable by everyone, except for a few sensitive folders where the application software by default locks down files.

2. The GROUP model
-------------------

**People in the same** Unix_group_ **as yourself can read your files**.
You can view your own ``userid`` (uid), ``groupid`` (gid) and Unix_group_ memberships with the ``id`` command.
Within this model:

* Everyone can read the files of other users in the **same** Unix_group_.

* Faculty, postdocs and Ph.D students are members of a Unix_group_ named ``xxxvip`` and can read this group's files.

  They can also read the M.Sc./B.Sc. students' files, if they are made secondary members of (added to) the ``xxxstud`` Unix_group_.

* M.Sc./B.Sc. students **cannot** read the files of the researchers, unless the specific folders are opened up.

In the above ``xxx`` refers to your research section abbreviation.

.. _Unix_group: https://docs.oracle.com/cd/E19120-01/open.solaris/819-2379/userconcept-35906/index.html

3. The PARANOID model
----------------------

**Noone else can read your files**.

Special access in the GROUP and PARANOID models
-----------------------------------------------

It is possible to open `specific folders` for access by either your Unix_group_, or by everyone.

Discussion point
----------------

What should be the default security model at Niflheim?
Individual users can override this if they so desire.

.. warning::

   Encrypt any files containing **real secrets**! If somebody gains root access to a computer that can mount Niflheim folders, they can access all your files!

.. raw:: pdf

   PageBreak

Technical stuff
==================

We recommend contacting **Niflheim Support** at <FYS-mail-Niflheim-support@dtu.dk> to set stuff up as you desire.
Below is the do-it-yourself description.

Implementing the OPEN, GROUP and PARANOID models without exceptions
-------------------------------------------------------------------

This is done by setting the appropriate permissions on your home folder.
If you do not need to have any open folders, locking down your home folder is sufficient.
But if you want to open `specific subfolders` you have to do a bit more work, see below.

Within the above 3 models you set the appropriate permission of your home folder with the chmod_command_:

.. code-block:: bash

   chmod og=rx $HOME     # 1. Open model, permission -rwxr-xr-x
   chmod g=rx,o= $HOME   # 2. Group model, permission -rwxr-x---
   chmod og= $HOME       # 3. Paranoid model, permission -rwx------

Note: the ``g`` and/or ``o`` in the permission string sets up permissions for your group and for other users.

Use the ls_command_ to view your folders:

.. code-block:: bash

   ls -l $HOME

.. _chmod_command: https://en.wikipedia.org/wiki/Chmod
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

If you are in group ``xxxvip`` you may want to make a `specific folder` readable by the Unix_group_ ``xxxstud``.

First you need to give `execute access` but `no read access` to your home folder,
and then people can access a file or folder *if they know its name AND have permissions to the file/folder*,
but they cannot list your file names.
Then you lock down all subfolders and files in your home folder. 
Finally, you can open the specific folder.

.. warning::

   This recipe only partly locks your home folder, as people still have execute access to it.
   This means that while people cannot run ``ls`` on your home folder, they can read some metadata about files and folders *if they can guess the file names* (you can ``stat`` the file).
   An AI can make very qualified guesses about file names!
   But only top-level folder names and file names can be verified in this way.

   PLEASE NOTE: If this is not acceptable, the only solution is to store the shared folder on a separate file system from your home folder.

Make your home folder **readable but not listable** for users outside ``xxxvip``:

.. code-block:: bash

   chmod o=x $HOME

Lock down all files and folders in your home folder:

.. code-block:: bash

   chmod o= $HOME/* $HOME/.??*

Make sure that any new files/folders created are also locked down by adding a umask_command_ line to your ``.bashrc`` file:

.. code-block:: bash

   umask 0027
   - or -
   umask u=rwx,g=rx,o=

.. _umask_command: https://en.wikipedia.org/wiki/Umask

Now you need to open your folder (here named ``shared``) with the desired permissions.

- To make the folder readable by **members of the group** ``xxxstud`` it must be assigned to that group,
  and the [setgid bit](https://en.wikipedia.org/wiki/Setuid) must be set so files in the folder are also assigned to the group:

  .. code-block:: bash

     chgrp -R xxxstud shared
     chmod g=rxs,o= shared

  Files and subfolders created in ``shared``, or copied there with the cp_command_,
  will belong to the ``xxxstud`` Unix_group_,
  but files `moved` there (with the mv_command_) need to have their group changed with the chgrp_command_ before they become readable by ``xxxstud``.

- To make a folder, subfolders or files **readable by everyone**,
  the folder and all its subfolders should be given read and execute permissions for everyone,
  and all files should be given read permission for everyone by using the find_command_:

  .. code-block:: bash

     chmod og+rx folder
     find folder -type f -exec chmod og+r {} +
     find folder -type d -exec chmod og+rx {} +

.. _cp_command: https://en.wikipedia.org/wiki/Cp_(Unix)
.. _mv_command: https://en.wikipedia.org/wiki/Mv_(Unix)
.. _chgrp_command: https://man7.org/linux/man-pages/man1/chgrp.1.html
.. _find_command: https://man7.org/linux/man-pages/man1/find.1.html
