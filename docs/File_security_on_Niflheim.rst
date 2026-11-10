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

The model is implemented by setting permissions on your home folder; permissions on its contents remain unchanged.

Run **one** of these commands:

.. code-block:: bash

   chmod og=rx "$HOME"     # OPEN: allow all users
   chmod g=rx,o= "$HOME"   # GROUP: allow the owning group
   chmod og= "$HOME"       # PRIVATE: allow only yourself

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

Checking access to a specific folder
------------------------------------

Use ``getfacl folder`` to check who has access.  Example:

.. code-block:: bash

   ~$ getfacl tmp
   # file: tmp
   # owner: schiotz
   # group: camdfac
   user::rwx
   group::r-x                # <--- The group camdfac has r-x access (Unix permissions)
   group:camdvip:r-x         # <--- The group camdvip also has access (through the ACL)
   mask::r-x
   other::---

Note that this folder named ``tmp`` belongs to the group ``camdfac`` which has access, but the group ``camdvip`` has also been given access.

Giving students access in the GROUP or PRIVATE models
-----------------------------------------------------

To share ``$HOME/shared`` with a student, replace ``username`` below with their login name.
The folder must already exist. Students can use the same recipe to share with supervisors.
You can also give access to an entire group by replacing ``u:username`` with ``g:groupname``.

.. warning::

   Traversing your home folder lets the recipient check guessed names and some metadata, even without listing its contents. An AI can make very qualified guesses about file names!

   If this is unacceptable, use of a shared storage space outside your home folder is necessary. Contact :ref:`Niflheim_support` in this case.

Before sharing, review access to your other files. To remove access through the "others" permissions
from all immediate files and folders, including hidden entries but excluding symbolic links:

.. code-block:: bash

   find "$HOME" -mindepth 1 -maxdepth 1 ! -type l -exec chmod o= {} +

This leaves group permissions and existing ACL_ grants unchanged.
Make sure that any new files/folders created (without a default ACL_) are also locked down by adding a `umask`_ line to your ``~/.bashrc`` file and run it in your current shell:

.. code-block:: bash

   umask 0027
   # or using symbolic notation:
   umask u=rwx,g=rx,o=

This allows group access but no access for others.

.. _umask: https://en.wikipedia.org/wiki/Umask

Before adding access, inspect existing ACL_ with ``getfacl "$HOME"`` and ``getfacl -R "$HOME/shared"``.
If you find unexpected ACL_ entries, contact :ref:`Niflheim_support` before proceeding,
otherwise you might accidentally grant more access than you intended!

Give the student traversal access to your home folder, then read access to the shared folder and its contents:

.. code-block:: bash

   setfacl -m u:username:x "$HOME"              # Traverse without listing names
   setfacl -R -m u:username:rX "$HOME/shared"   # Access existing contents
   setfacl -R -m d:u:username:rX "$HOME/shared" # Inherit access for new contents

``-R`` applies recursively; ``X`` grants execute access to directories and files already executable by anyone.
The ``d:`` prefix sets default ACL_ on all existing directories.
New entries inherit access, limited by the permissions requested by the creating application.
Group ownership stays unchanged.

Check the result:

.. code-block:: bash

   getfacl "$HOME" "$HOME/shared"

Give the student the full path to the shared folder and ask them to check that they can read its files.
For nested shared folders, they also need ``x`` on every intermediate folder.

If ``getfacl`` shows ``user:username:r-x  #effective:r--``, the ACL_ mask is blocking execute access.
Changing group permissions with ``chmod`` changes this mask and can affect the student's access.

**Moving or copying files into the shared folder:** default ACL_ apply when entries are created,
but if you move exitisting folders or files into the shared folder they keep their original permissions:

* ``mv`` preserves existing permissions and ACL_, so moved files and folders do not inherit the shared folder's access settings.
* Plain ``cp`` normally lets newly created entries inherit the destination's default ACL_, limited by the source permissions.
  However, ``cp -p`` and ``cp -a`` preserve source permissions and ACL_ instead, so the student may lose access to the copied files.
* ``rsync -a`` preserves source permission bits, which can limit the student's access through the ACL_ mask.
  It does **not** copy source ACL_ unless you also use ``-A`` (``--acls``); that option replaces destination ACL_ with source ACL_.

After moving files with ``mv`` or copying with preserved permissions or ACL_,
rerun both recursive ``setfacl`` commands above to grant access to the imported files and set inheritance on imported folders.
Ask the student to check access again after the transfer.

Sharing with everyone
---------------------

To allow everyone to read a folder and its contents:

.. code-block:: bash

   chmod -R og+rX folder

Everyone also needs traversal (``x``) permission on every parent folder.

