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

Apart from the home folder, access to a file requires permission on the file itself and traversal permission on every parent folder.
You can restrict individual folders or grant access to specific folders using *Access Control Lists* (ACL_).

Faculty belong to the group ``xxxfac``, postdocs and Ph.D. students to ``xxxvip``, and M.Sc. / B.Sc. students to ``xxxstud`` where ``xxx`` is the research section abbreviation.
Under the GROUP or PRIVATE models, students and supervisors can easily grant each other access to selected folders using ACL_.
Run ``id`` to see your user ID and group memberships.

Checking your security model
----------------------------

To view your current security model, run the script ``showsecuritymodel``.  Be particularly aware of any warnings issued.  The script will report the security models of your home folder and any other file systems you may have access to.  Example:

.. code-block::

   $ ./showsecuritymodel
   Folder /home/niflheim/myusername has file access model GROUP (groupname)

   Folder /home/niflheim2/myusername has file access model PRIVATE

   Folder /home/scratch3/myusername has file access model GROUP (groupname)
   Folder is TRAVERSIBLE for all users
   WARNING: Folder is not readable for all users, but these subfolders are:
         /home/scratch3/myusername/somefolder  (r-x)
         /home/scratch3/myusername/someotherfolder  (r-x)

   Folder /scratch/myusername has file access model OPEN


.. _Unix group: https://docs.oracle.com/cd/E19120-01/open.solaris/819-2379/userconcept-35906/index.html

.. warning::

   Encrypt any files containing **real secrets**! If somebody gains root access to a computer that can mount Niflheim folders, they can access all your files!

For example, the *GNU Privacy Guard* (GPG_ or gpg) tool is a native/baseos security tool for encrypting files. 

.. raw:: pdf

   PageBreak

.. _ACL: https://www.redhat.com/en/blog/linux-access-control-lists
.. _GPG: https://www.redhat.com/en/blog/encryption-decryption-gpg

Technical details
==================

We recommend contacting :ref:`Niflheim_support` to set data access up as you desire.
Below is the do-it-yourself description.

Permission basics
-----------------

Standard Unix permissions apply to the owner (``u`` for *user*), owning group (``g`` for *group*), and everyone else (``o`` for *other*):

.. list-table::
   :header-rows: 1

   * - Permission
     - Files
     - Folders
   * - Read (``r``)
     - Read contents
     - List names
   * - Write (``w``)
     - Modify contents
     - Create, delete or rename entries (also requires ``x``)
   * - Execute (``x``)
     - Run as a program
     - Traverse the folder to access entries

Access to a file requires permission on the file itself and ``x`` on every parent folder.
Use |chmod|_ to change standard permissions; inspect them with ``ls -l file`` or ``ls -ld folder``.

*Access Control Lists* (ACL_) grant permissions to additional users or groups without changing group ownership.
Use |setfacl|_ to change ACL_ and |getfacl|_ to inspect them.
You can set a default ACL_ on a folder, then newly created files and subfolders inherit those permissions.

For introductions, see `Unix file permissions <https://compenv.phys.ethz.ch/linux/basics_1/31_basic_permissions/>`_
and `Managing file access with ACLs <https://docs.rc.fas.harvard.edu/kb/acls-facls/>`_.

.. |chmod| replace:: ``chmod``
.. _chmod: https://en.wikipedia.org/wiki/Chmod
.. |setfacl| replace:: ``setfacl``
.. |getfacl| replace:: ``getfacl``
.. _setfacl: https://man7.org/linux/man-pages/man1/setfacl.1.html
.. _getfacl: https://man7.org/linux/man-pages/man1/getfacl.1.html

Implementing a model by setting home-folder permissions
-------------------------------------------------------

The model is implemented by setting permissions on your home folder; permissions on its contents may remain unchanged unless you later intend to share subfolders.

For the OPEN model, run this command:

.. code-block:: bash

   chmod og=rx "$HOME"     # OPEN: allow all users

For the GROUP model, run the first line below.  If you later intend to share subfolders you should also run the second line, it takes away permissions on subfolders and files so you don't accidentally open them up (the file sharing script below should catch that, however).

.. code-block:: bash

   chmod g=rx,o= "$HOME"   # GROUP: allow the owning group
   find "$HOME" -mindepth 1 -maxdepth 1 ! -type l -exec chmod o= {} +

For the PRIVATE mode, run the first line below. If you later intend to share subfolders you should also run the second line.

.. code-block:: bash

   chmod og= "$HOME"       # PRIVATE: allow only yourself
   find "$HOME" -mindepth 1 -maxdepth 1 ! -type l -exec chmod go= {} +

These commands leave your own permissions unchanged. Run ``getfacl "$HOME"`` to check the result.
To share selected subfolders under GROUP or PRIVATE, follow the sharing recipe below.

Closing specific folders in the OPEN or GROUP models
----------------------------------------------------

You can always close off a folder so it is either private-only, or only accessible to your group.
You do that by:

.. code-block:: bash

   chmod g=rx,o= folder  # Only the group can read/list the folder
   chmod og= folder      # Only the owner can access the folder

Check with ``getfacl folder`` if any named user or group still has access through an ACL_.
If so remove the ACL_ with ``setfacl -x u:username folder`` or ``setfacl -x g:groupname folder``.


The ``manageaccess`` script, for sharing folders with others
------------------------------------------------------------

We provide a script named ``manageaccess`` to help you share folders with other users.  The script has the following subcommands

``manageaccess overview``
   Gives an overview of what you are sharing now.

``manageaccess show``
  Shows who you have given access to a specific folder (see next section).

``manageaccess add``
  Share a given folder with another user or group, see sections below.

``manageaccess remove``
  Revoke access for another user (or all other users)

The ``manageaccess`` script acts a a front-end for the ``getfacl`` and ``setfacl`` unix commands.


Checking access to a specific folder
------------------------------------

Use ``manageaccess show folder`` to check who has access.  It also works on individual files.  Example:

.. code-block:: bash

   ~$ manageaccess show tmp
   # file: tmp
   # owner: username
   # group: camdfac
   user::rwx
   group::r-x                # <--- The group camdfac has r-x access (Unix permissions)
   group:camdvip:r-x         # <--- The group camdvip also has access (through the ACL)
   mask::r-x
   other::---

   /home/niflheim/username TRAVERSIBLE (not readable for: group:camdvip)

Note that this folder named ``tmp`` belongs to the group ``camdfac`` which has access, but the group ``camdvip`` has also been given access.  However, the latter group cannot read the parent folder, but they can traverse it: they need to know the name of the shared folder to access it.


Giving other users access in the GROUP or PRIVATE model
-------------------------------------------------------


.. warning::

   Giving acccess to a subfolder of your home folder allows the users to traverse your home folder.  Traversing your home folder lets the user check guessed names and some metadata, even without listing its contents. An AI can make very qualified guesses about file names!

   If this is unacceptable, use of a shared storage space outside your home folder is necessary. Contact :ref:`Niflheim_support` in this case.


Use ``manageaccess add -N|-R|-F SPECIFICATION ITEM [ITEM2 ...]`` to give individual users or user groups access to an item (folder or file).  Example:

.. code-block:: bash

   manageaccess add -R u:mystudent:rx sharedfolder1 sharedfolder2

You specify which user/group to give access with a specification string: ``u:username:rx`` or ``g:groupname:rx``.  The ``rx`` part of the string indicates that you intend to give read access to files (and execute access *if and only if* they already are executable), and that you both give the right to list a folder (``r``) and access its content (``x``).

For a folder, you need to speciy whether you want to give access to all subfolders (``-R`` or ``--recursive``), only to the folder and its files but not to subfolders (``-N`` or ``--non-recursive``), or only to the folder itself but not any of its content (``-F`` or ``--folder-only``).  The latter is probably not very useful.

If you want to give access to more than one user or group, separate specifications by a comma but no space: ``u:user1:rx,u:user2:rx,g:group1:rx``. The ITEM can be a folder or a file, several folders or files can be specified by separating them with a space.

The script will open traversal access to all parent folders so the relevant users can reach the folder you are opening.  The script checks if giving traversal access to parent folders would open access to other folders as a side effect, if that is the case it aborts without changing any permissions.

.. warning::

   Do not give write access (``w``) to files or folders unless you **really** know what you are doing.  Even if you trust the other person, accidents easily happen.  There are almost always better ways to collaborate (version control for code, cloud based solutions for documents).


Revoking access to other users
------------------------------

To revoke access, use the ``manageaccess remove SPECIFICATION ITEM [ITEM2 ...]`` command.  Example:

.. code-block:: bash

   manageaccess remove u:mystudent:rx sharedfolder1 sharedfolder2

The command will also revoke access to all subfolders and files in a folder, unless you speficy otherwise by giving the ``-F`` or ``-N`` options (see the ``manageaccess add`` command above).

If the parent folder(s) give traversal access to the user and no other shared folders require that traversal access is preserved, the traversal access of the parent folder(s) is also revoked.

You can also remove access to all other users with the command

.. code-block:: bash

   manageaccess remove --all sharedfolder1 sharedfolder2


Sharing with everyone
---------------------

To allow everyone to read a folder and its contents:

.. code-block:: bash

   chmod -R og+rX folder

Everyone also needs traversal (``x``) permission on every parent folder.

