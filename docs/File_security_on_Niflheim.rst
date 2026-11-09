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

`chmod`
+++++++

The standard method for controlling the permission bits in unix. The set permissions are visible by running `ls -l` on a file or `ls -ld` on a folder.

`setfacl`
+++++++++

Sets a possibly more fine-grained control of access. Access can be granted to single users instead of full user groups. The ACL permissions are visible by running `getfacl <FILE-OR-FOLDER>`.

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

If you are in group ``xxxvip`` you may want to make a `specific folder` readable by the `Unix group`_ ``xxxstud``.

First you need to give `execute access` but `no read access` to your home folder,
and then people can access a file or folder *if they know its name AND have permissions to the file/folder*,
but they cannot list your file names.
Then you lock down all subfolders and files in your home folder. 
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
for users outside ``xxxvip``:

.. code-block:: bash

   chmod o=x $HOME

Lock down all files and folders in your home folder:

.. code-block:: bash

   chmod o= $HOME/* $HOME/.??*

Make sure that any new files/folders created are also locked down by adding a umask_command_ line to your ``.bashrc`` file:

.. code-block:: bash

   umask 0027
   # or using symbolic notation:
   umask u=rwx,g=rx,o=

.. _umask_command: https://en.wikipedia.org/wiki/Umask

Now you need to open your folder (in this example named ``shared``) with the desired permissions:

- To make the folder readable by **members of the group** ``xxxstud`` it must be assigned to that group,
  and the `setgid bit`_ must be set so files in the folder are also assigned to the group:

  .. code-block:: bash

     chgrp -R xxxstud shared
     chmod g=rxs,o= shared

  Files and subfolders created in ``shared``, or copied there with the `cp command`_,
  will belong to the ``xxxstud`` `Unix group`_,
  but files `moved` there (with the `mv command`_) need to have their group changed with the `chgrp command`_ before they become readable by ``xxxstud``.

- To make a folder, subfolders or files **readable by everyone**,
  the folder and all its subfolders should be given read and execute permissions for everyone,
  and all files should be given read permission for everyone by using the `find command`_:

  .. code-block:: bash

     chmod og+rx folder
     find folder -type f -exec chmod og+r {} +
     find folder -type d -exec chmod og+rx {} +

.. _setgid bit: https://en.wikipedia.org/wiki/Setuid
.. _cp command: https://en.wikipedia.org/wiki/Cp_(Unix)
.. _mv command: https://en.wikipedia.org/wiki/Mv_(Unix)
.. _chgrp command: https://man7.org/linux/man-pages/man1/chgrp.1.html
.. _find command: https://man7.org/linux/man-pages/man1/find.1.html
