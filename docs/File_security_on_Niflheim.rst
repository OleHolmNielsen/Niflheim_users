.. _file-security-on-niflheim:

=========================
File security on Niflheim
=========================

Overview
===========

According to DTU's `IT security`_ policies *Appendix B*, home directories are personal and private.
**Access to another user's home directory requires explicit agreement**, even if file permissions allow it.

We recommend the **GROUP model** for a balance between security and ease of collaboration.
Each research section should choose a model appropriate to its data, with the section leader responsible for the decision.

Please contact the :ref:`Niflheim_support` if you would like further advice.

.. _IT security: https://www.inside.dtu.dk/informationshaandtering/informationssikkerhed

Choose a security model
-----------------------

.. list-table::
   :header-rows: 1
   :widths: 25 75

   * - Model
     - Who may access your home directory
   * - 1. OPEN
     - All Niflheim users (current default; not recommended).
   * - 2. GROUP
     - You and members of your home directory's `Unix group`_ (recommended).
   * - 3. PRIVATE
     - You only, apart from administrators with privileged access.

Apart from the home folder access to individual files also depends on their permissions and any access control lists (ACLs).
You can restrict individual folders or grant exceptions using ACLs.

Faculty, postdocs and Ph.D. students belong to ``xxxvip``, where ``xxx`` is the research section abbreviation.
Under the GROUP or PRIVATE models, students and supervisors can easily grant each other access to selected folders using ACLs.
Run ``id`` to see your user ID and group memberships.

.. _Unix group: https://docs.oracle.com/cd/E19120-01/open.solaris/819-2379/userconcept-35906/index.html

.. warning::

   Encrypt any files containing **real secrets**! If somebody gains root access to a computer that can mount Niflheim folders, they can access all your files!

To Do: Provice examples on how to encrypt folders.

.. raw:: pdf

   PageBreak

Technical details
==================

We recommend contacting :ref:`Niflheim_support` to set data access up as you desire.
Below is the do-it-yourself description.

Access to files and folders are handled with `unix file permissions <https://compenv.phys.ethz.ch/linux/basics_1/31_basic_permissions/>`_ and `access control lists (ACL) <https://docs.tacc.utexas.edu/tutorials/acls/>`_

Tools for setting permissions
-----------------------------

|chmod|_
++++++++

Sets the standard Unix permissions for the owner, owning group and everyone else.
Inspect them with ``ls -l file`` or ``ls -ld folder``.
For folders, ``r`` allows listing names, ``x`` allows accessing entries, and ``w`` together with ``x`` allows creating or deleting entries.

.. |chmod| replace:: ``chmod``
.. _chmod: https://en.wikipedia.org/wiki/Chmod

|setfacl|_ and |getfacl|_
+++++++++++++++++++++++++

ACLs grant permissions to additional users or groups without changing the owning group.
Use |setfacl|_ to change them and |getfacl|_ to inspect them:

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

.. |setfacl| replace:: ``setfacl``
.. |getfacl| replace:: ``getfacl``
.. _setfacl: https://man7.org/linux/man-pages/man1/setfacl.1.html
.. _getfacl: https://man7.org/linux/man-pages/man1/getfacl.1.html

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
     setfacl -R -m d:u:username:rX shared  # Set inheritance on all existing directories

  New files and subfolders inherit the default ACL, limited by the permissions requested by the creating application.
  These ACL commands do not change group ownership.
  Files copied with the `cp command`_ may inherit the default ACL, but options such as ``-p`` or ``-a`` preserve source permissions and ACLs instead.
  Files moved with the `mv command`_ retain their ACLs; rerun the two commands above after moving files or copying with preserved permissions.
  Check effective access with ``getfacl``.

- To make a folder, subfolders or files **readable by everyone**,
  the folder and all its subfolders should be given read and execute permissions for everyone,
  and all files should be given read permission for everyone by using the `find command`_:

  .. code-block:: bash

     chmod -R og+rX folder

.. _cp command: https://en.wikipedia.org/wiki/Cp_(Unix)
.. _mv command: https://en.wikipedia.org/wiki/Mv_(Unix)
.. _chgrp command: https://man7.org/linux/man-pages/man1/chgrp.1.html
.. _find command: https://man7.org/linux/man-pages/man1/find.1.html
