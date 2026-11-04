File security on Niflheim
=========================

In light of the possibilities to use AI for automated espionage/snooping we need to consider the amount of openness on Niflheim. Three models can easily be implemented.

The OPEN model (the current default)
------------------------------------

All files are readable by everyone, except for a few sensitive folders where software automatically lock down files. Any user of Niflheim can read your files, as part of a collaboration or with more nefarious purposes. Specific folders can be protected further.

The GROUP model
---------------

People in the same Unix group can read your files. Employees including PhD students are members of a group named ``xxxvip``. M.Sc. and B.Sc. students are member of the corresponding ``xxxstud`` group, and supervisors (incl Ph.D. students???) are secondary members of that group too. Within this model, faculty, postdocs and Ph.D students can read each other's files, and they can read the M.Sc./B.Sc. students' files, but M.Sc./B.Sc. students cannot read the files of the researchers, unless specific folders are opened up.

The PARANOID model
------------------

Nobody can read your files.

Special access in the GROUP and PARANOID models
-----------------------------------------------

It is possible to open folders for access by either your group or everyone.

Discussion point
----------------

What should be the default security model at Niflheim? Individual users can override this if they so desire.

.. warning::

   Encrypt real secrets! If somebody gains root access to a desktop that can mount Niflheim folders, they can access your files!

.. raw:: pdf

   PageBreak

Technical stuff
===============


We recommend contacting Ole or Steen to set stuff up as you desire. Below is the do-it-yourself description.

Implementing the OPEN, GROUP and PARANOID models without exceptions
-------------------------------------------------------------------

This is done by setting the right permissions on your home folder. If you have no open folders, locking down your home folder is sufficient. If you want to open specific subfolders you have to do a bit more, see below.

You set the right permission of your home folder like this:

.. code-block:: bash

   chmod og=rx $HOME     # Open model, permission -rwxr-xr-x
   chmod g=rx,o= $HOME   # Group model, permission -rwxr-x---
   chmod og= $HOME       # Paranoid model, permission -rwx------

Note: the ``g`` and/or ``o`` in the permission string sets up permissions for your group and for other users.

Closing specific folders in the OPEN or GROUP models
----------------------------------------------------

You can always close off a folder so it is either only private or only accessible to your group. You do that by

.. code-block:: bash

   chmod g=rx,o= folder  # Only the group can read/list the folder
   chmod og= folder      # Nobody can access the folder

Giving students access in the GROUP model
-----------------------------------------

If you are in group ``xxxvip`` you may want to make a folder readable for ``xxxstud``. First you need to give execute access but no read access to your home folder, then people can access a file or folder *if they know its name AND have permissions to the file/folder*, but they cannot list your file names. Then you lock down all subfolders and files in your home folder.  Finally, you can open the specific folder.

.. warning::

   This recipe only partly locks your home folder, as people still have execute access to it.  This means that while people cannot run ``ls`` on your home folder, they can read some metadata about files and folders *if they can guess the file names* (you can ``stat`` the file).  An AI can make very qualified guesses about file names!  But only top-level folder names and file names can be verified in this way.

   If that is not acceptable, the only solution is to have the shared folder on a separate file system from your home folder.


Make your home folder readable but not listable for people outside ``xxxvip``:

.. code-block:: bash

   chmod o=x $HOME

Lock down all files and folders in your home folder:

.. code-block:: bash

   chmod o= $HOME/* $HOME/.??*

Make sure that any new files/folders created are also locked down by adding this line to your ``.bashrc`` file:

.. code-block:: bash

   umask 0027   # Alternative syntax: umask u=rwx,g=rx,o=

Now you need to open your folder (here named ``shared``) with the desired permissions.

- To make the folder readable by members of the group ``xxxstud`` it must be assigned to the group and the "setgid bit" must be set so files inside are also assigned to the group:

  .. code-block:: bash

     chgrp -R xxxstud shared
     chmod g=rxs,o= shared

  Files and subfolder created in ``shared`` or copied there with ``cp`` will belong to ``xxxstud``, but files moved there (with ``mv``) need to have their group changed with ``chgrp`` before they are readable by ``xxxstud``.

- To make a folder, subfolders and files readable by everyone, the folder and all subfolders should have read and execute permissions for everyone, and files should have read permission for everyone:

  .. code-block:: bash

     chmod og+rx folder
     find folder -type f -exec chmod og+r {} +
     find folder -type d -exec chmod og+rx {} +

