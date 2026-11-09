.. _file-security-on-niflheim:

=========================
File security on Niflheim
=========================

Overview
===========

According to DTU's `IT security`_ policies *Appendix B*, user home directories are personal and private,
and you are **not permitted** to access in any way the directories of other users,
even if file permissions would make this technically possible,
except by explicit agreement.

In light of the possibilities to use AI for automated espionage/snooping we need to reconsider the amount of openness on Niflheim.
Three models of file security can fairly easily be implemented as discussed below.

On Niflheim we recommend model **2. The GROUP model** as a convenient compromise between security and ease of use.
However, each research section should decide which model is appropriate for them in view of the sensitivity of their data,
and the section leader should take responsibility for the decision.

Please contact the :ref:`Niflheim_support` if you would like further advice.

.. _IT security: https://www.inside.dtu.dk/informationshaandtering/informationssikkerhed

1. The OPEN model (the current default - not recommended))
-----------------------------------------------------------------

**Any user of Niflheim can read your files**,
as part of a collaboration, or with more nefarious purposes.
Specific folders can be protected further if desired.

All files are readable by everyone, except for a few sensitive folders where the application software by default locks down files.

2. The GROUP model
-------------------

**People in the same** `Unix group`_ **as yourself can read your files**.
You can view your own ``userid`` (uid), ``groupid`` (gid) and `Unix group`_ memberships with the ``id`` command.
Within this model:

* Everyone can read the files of other users in the **same** `Unix group`_.

* Faculty, postdocs and Ph.D students are members of a `Unix group`_ named ``xxxvip`` and can read this group's files.

  They can also read the M.Sc./B.Sc. students' files, if they are given access to those folders using access control lists. 

* M.Sc./B.Sc. students **cannot** read the files of the researchers, unless the specific folders are opened up.

* Selected folders can be protected further if desired.

In the above ``xxx`` refers to your research section abbreviation.

.. _Unix group: https://docs.oracle.com/cd/E19120-01/open.solaris/819-2379/userconcept-35906/index.html

3. The PRIVATE model
----------------------

**Noone else can read your files**.

Special access in the GROUP and PRIVATE models
-----------------------------------------------

It is possible to open `specific folders` for access to specific users with access control lists or to your `Unix group`_, or to everyone.

.. warning::

   Encrypt any files containing **real secrets**! If somebody gains root access to a computer that can mount Niflheim folders, they can access all your files!

To Do: Provice examples on how to encrypt folders.

.. raw:: pdf

   PageBreak

Technical details
==================

We recommend contacting :ref:`Niflheim_support` to set data access up as you desire.
Below is the do-it-yourself description.

Tools for setting permissions
-----------------------------

``chmod``
+++++++++

Sets the standard Unix permissions for the owner, owning group and everyone else.
Inspect them with ``ls -l file`` or ``ls -ld folder``.
For folders, ``r`` allows listing names, ``x`` allows accessing entries, and ``w`` together with ``x`` allows creating or deleting entries.

``setfacl`` and ``getfacl``
+++++++++++++++++++++++++

ACLs grant permissions to additional users or groups without changing the owning group.
Use ``setfacl`` to change them and ``getfacl`` to inspect them:

.. code-block:: bash

   setfacl -m u:username:--x "$HOME"    # Allow traversal of your home folder
   setfacl -m u:username:r-x shared     # Allow listing and entering shared
   setfacl -m u:username:r-- shared/file # Allow reading a specific file
   getfacl shared
   setfacl -x u:username shared         # Remove this user's ACL entry

Replace ``username`` with the intended user; use ``g:groupname`` for a group.
Access requires ``x`` permission on every parent folder as well as permission on the target.
These commands affect only the named file or folder, not its contents.

A default ACL, such as ``setfacl -m d:u:username:r-x shared``, supplies inherited permissions for newly created entries;
it does not change existing entries or files moved into the folder.
New files normally inherit read access without execute permission.

The ACL mask limits the effective permissions of named users and all groups.
Check ``getfacl`` for ``effective:`` annotations: ``setfacl`` normally recalculates the mask,
and later changes to group permissions with ``chmod`` also change it.

Implementing the OPEN, GROUP and PRIVATE models without exceptions
-------------------------------------------------------------------

This is done by setting the appropriate permissions on your home folder.
If you do not need to have any open folders, locking down your home folder is sufficient.
But if you want to open `specific subfolders` you have to do a bit more work, see below.

Within the above 3 models you set the appropriate permission of your home folder with the `chmod command`_:

.. code-block:: bash

   chmod og=rx $HOME     # 1. Open model, permission -rwxr-xr-x
   chmod g=rx,o= $HOME   # 2. Group model, permission -rwxr-x---
   chmod og= $HOME       # 3. Private model, permission -rwx------

Note: the ``g`` and/or ``o`` in the permission string sets up permissions for your group and for other users.

Use the `ls command`_ to view your folders:

.. code-block:: bash

   ls -ld $HOME

.. _chmod command: https://en.wikipedia.org/wiki/Chmod
.. _ls command: https://en.wikipedia.org/wiki/Ls

Closing specific folders in the OPEN or GROUP models
----------------------------------------------------

You can always close off a folder so it is either private-only, or only accessible to your group.
You do that by:

.. code-block:: bash

   chmod g=rx,o= folder  # Only the group can read/list the folder
   chmod og= folder      # Nobody can access the folder

Giving students access in the GROUP model
-----------------------------------------

If you are in group ``xxxvip`` you may want to make a `specific folder` readable by a student.

First you need to give `execute access` but `no read access` to your home folder,
and then the student can access a file or folder *if they know its name AND have permissions to the file/folder*,
but they cannot list your file names.
If you want to guard against possible access to other files in your home folder, you need to lock down all other subfolders and files in your home folder. 
Finally, you can open the specific folder.

.. warning::

   This recipe only partly locks your home folder, as people still have execute access to it.
   This means that while people cannot run ``ls`` on your home folder,
   they can read some metadata about files and folders *if they can guess the file names*
   (they can ``stat`` the file).
   An AI can make very qualified guesses about file names!
   But only top-level folder names and file names can be verified in this way.

   PLEASE NOTE: If this is not acceptable, the only solution is to store the shared folder on a separate file system from your home folder.

Make your home folder **accessible but not listable** (i.e., contents cannot be seen)
for a user outside ``xxxvip``:

.. code-block:: bash

   setfacl -m u:username:x $HOME

Optionally lock down all files and folders in your home folder:

.. code-block:: bash

   find "$HOME" -mindepth 1 -maxdepth 1 -exec chmod o= {} +

Make sure that any new files/folders created are also locked down by adding a umask_command_ line to your ``.bashrc`` file:

.. code-block:: bash

   umask 0027
   # or using symbolic notation:
   umask u=rwx,g=rx,o=

.. _umask_command: https://en.wikipedia.org/wiki/Umask

Now you need to open your folder (in this example named ``shared``) with the desired permissions:

- To make the folder readable by a user, set the modify the ACL and set the default also:

  .. code-block:: bash

     setfacl -R -m u:username:rX shared  # Read access; execute for directories and existing executables
     find shared -type d -exec setfacl -m d:u:username:rx {} +  # Set inheritance on all existing directories

  New files and subfolders inherit the default ACL, limited by the permissions requested by the creating application.
  These ACL commands do not change group ownership.
  Files copied with the `cp command`_ may inherit the default ACL, but options such as ``-p`` or ``-a`` preserve source permissions and ACLs instead.
  Files moved with the `mv command`_ retain their ACLs; rerun the two commands above after moving files or copying with preserved permissions.
  Check effective access with ``getfacl``.

- To make a folder, subfolders or files **readable by everyone**,
  the folder and all its subfolders should be given read and execute permissions for everyone,
  and all files should be given read permission for everyone by using the `find command`_:

  .. code-block:: bash

     chmod og+rx folder
     find folder -type f -exec chmod og+r {} +
     find folder -type d -exec chmod og+rx {} +

.. _cp command: https://en.wikipedia.org/wiki/Cp_(Unix)
.. _mv command: https://en.wikipedia.org/wiki/Mv_(Unix)
.. _chgrp command: https://man7.org/linux/man-pages/man1/chgrp.1.html
.. _find command: https://man7.org/linux/man-pages/man1/find.1.html
