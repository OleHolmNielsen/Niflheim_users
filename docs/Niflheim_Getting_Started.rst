.. _Niflheim_Getting_Started:

========================
Niflheim Getting Started
========================

View the page contents in the menu bar on the left.
See the :ref:`Access_to_Niflheim` section about getting access.

Overview of Niflheim
====================

The *Niflheim* Linux cluster setup is based upon:

* Compute nodes as described on the :ref:`Hardware` page.
* RockyLinux_ 8 and AlmaLinux_ 8 OS.
* Slurm_ batch queueing system.
* Software_Modules_ using Lmod_ and EasyBuild_modules_.

Niflheim usage accounting_reports_ (access restricted to the DTU network).

User support: Please see the :ref:`Niflheim_support` page.

.. _CentOS: https://www.centos.org/
.. _AlmaLinux: https://almalinux.org/
.. _RockyLinux: https://rockylinux.org/
.. _Slurm: https://www.schedmd.com/
.. _EasyBuild_modules: https://wiki.fysik.dtu.dk/Niflheim_system/EasyBuild_modules/
.. _accounting_reports: https://wiki.fysik.dtu.dk/graphs/accounting_reports.html

Login to Niflheim
=================

See the :ref:`Access_to_Niflheim` section about getting access.
Login to *Niflheim* is available using SSH_ **only** from within the DTU network.
If you are outside of DTU, please log in to the DTU_VPN_ service or the G-databar_ first.

.. _G-databar: https://www.gbar.dtu.dk/

It is recommended to login to the node type identical to the compute nodes onto which you submit batch jobs,
see :ref:`compute_node_partitions` and the :ref:`Hardware` page.
This rule will ensure that if you build your own compiled code,
it is going to run only on compatible compute node hardware,
see :ref:`binary_compiled_code`.

Niflheim's login nodes are:

* ``sylg.fysik.dtu.dk``, ``slid.fysik.dtu.dk``, and ``slid2.fysik.dtu.dk``:
 
  * Login node for partition ``xeon24el8`` (RockyLinux_ 8 OS).
  * 24 CPU cores (Intel Xeon CPU E5-2650 v4 @ 2.20GHz Broadwell_), 256 GB of RAM memory.
  * Refer to this as CPU_ARCH= **broadwell_el8**.

* ``svol.fysik.dtu.dk`` and ``thul.fysik.dtu.dk``:

  * Login node for partitions ``xeon40el8`` as well as ``sm3090el8`` (AlmaLinux_ / RockyLinux_ 8 OS).
  * Intel Xeon Scalable Gold CPU 5115 @ 2.40GHz Skylake_ with AVX512_ vector instructions.
  * Refer to this as CPU_ARCH= **skylake_el8**.

* ``surt.fysik.dtu.dk``:

  * Login node for partitions ``xeon56``, ``xeon32_4096``, and ``a100`` (AlmaLinux_ / RockyLinux_ 8 OS).
  * Please build all applications for xeon56 with the latest Intel MKL_ math library (see `Software environment modules`_ below)!
  * 56 CPU cores (Intel Xeon Gold 6348 CPU @ 2.60GHz IceLake_ with AVX512_ vector instructions), 512 GB of RAM.
  * Refer to this as CPU_ARCH= **icelake**.

* ``fjorm.fysik.dtu.dk``:

  * Login node for partition ``epyc96`` (RockyLinux_ 8 OS).
  * Please build all applications for ``epyc96`` with the latest ``foss`` toolchain (see `Software environment modules`_ below)!
  * 16 CPU cores (AMD EPYC 9124 *Genoa* Zen4_; note that the **epyc96** partition have 96 CPU cores), 384 GB of RAM.
  * Refer to this as CPU_ARCH= **epyc9004**.

The login nodes ``fjorm``, ``surt``, ``svol``, ``sylg``, and ``thul`` must not be overloaded with heavy tasks, since this will disturb other users.

The login nodes ``slid2`` and ``slid`` would be acceptable for more heavy testing of codes, but please bear in mind that the login nodes may be shared by many users, and no single user should monopolize any login nodes.
**Long tasks should always be submitted as batch jobs**.

.. _Hyperthreading: https://en.wikipedia.org/wiki/Hyper-threading
.. _AVX512: https://en.wikipedia.org/wiki/AVX-512
.. _MKL: https://en.wikipedia.org/wiki/Math_Kernel_Library
.. _AVX: https://en.wikipedia.org/wiki/Advanced_Vector_Extensions
.. _AVX2: https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#Advanced_Vector_Extensions_2
.. _SSH: https://en.wikipedia.org/wiki/Secure_Shell
.. _IceLake: https://en.wikipedia.org/wiki/Ice_Lake_(microprocessor)
.. _Cascade_Lake: https://en.wikipedia.org/wiki/Cascade_Lake_(microarchitecture)
.. _Skylake: https://en.wikipedia.org/wiki/Skylake_(microarchitecture)
.. _Broadwell: https://en.wikipedia.org/wiki/Broadwell_%28microarchitecture%29
.. _Zen4: https://en.wikipedia.org/wiki/Zen_4
.. _NVLink: https://en.wikipedia.org/wiki/NVLink
.. _A100: https://www.nvidia.com/en-us/data-center/a100/

SSH setup
---------

The SSH_ (*Secure Shell*) communication protocol is described widely on the Internet.
The user's SSH_ configuration files and `cryptographic keys <https://www.ssh.com/academy/ssh/public-key-authentication>`_
are stored in the directory ``$HOME/.ssh/`` on Linux systems.

You are encouraged to configure SSH_ keys on your own PC so that you can login to Niflheim without entering a password:

* Linux users: See for example 
  `Creating an SSH Key Pair and Configuring Public Key Authentication on a Server <https://www.linode.com/docs/guides/use-public-key-authentication-with-ssh/>`_.

* MacOS users: See for example `Manually generating your SSH key in macOS
  <https://docs.joyent.com/public-cloud/getting-started/ssh-keys/generating-an-ssh-key-manually/manually-generating-your-ssh-key-in-mac-os-x>`_.

* Windows users can use the free PuTTY_ SSH_ client and read the instructions
  `Using public keys for SSH authentication <https://the.earth.li/~sgtatham/putty/0.76/htmldoc/Chapter8.html#pubkey>`_.
  The Windows PC should run PuTTY_ 's application `Pageant <https://the.earth.li/~sgtatham/putty/0.76/htmldoc/Chapter9.html#pageant>`_ 
  for authentication.

* Windows users may alternatively install the graphical MobaXterm_ X server and SSH client.
  **Warning**: MobaXterm_ has a *Remote Monitoring* feature that probes the login node every second so that it can display a remote status bar at the bottom of the terminal window.
  It is not on by default, and we request that you **do not use** it because it overloads the login nodes!

The SSH **public key** from your PC can be appended to the file ``$HOME/.ssh/authorized_keys`` to enable password-less logins.

**WARNING:** DO NOT copy SSH keys from Niflheim to any external computer (for example, your PC) for reasons of security!
The Niflheim SSH keys must only be used on the Niflheim system.

**Optional**: You may create SSH keys using this command on any Niflheim login node::

  authorized_keys

The SSH_ key files will be created in the directory ``$HOME/.ssh/``.
This can be necessary if you use commercial MPI libraries which use SSH in stead of the recommended Slurm_ method for starting tasks.

.. _PuTTY: https://www.chiark.greenend.org.uk/~sgtatham/putty/
.. _MobaXterm: https://mobaxterm.mobatek.net/

Home directory and disk quota
=============================

Every user has a personal home directory on one of the Niflheim file servers, located in a file system allocated to the research group (for example, ``/home/energy/``).

The home directory file servers have a **daily backup** of all changed files.
To request a manual restore of lost files, please send mail to the address in the :ref:`Niflheim_support` page.

To view your current disk quota::

  quota -s

To view file systems mounted on the node (omitting temporary file systems)::

  df -Phx tmpfs

.. _binary_compiled_code:

Usage of binary compiled code
=============================

Users of Niflheim should be aware of some important facts about **different CPU types**.
More recent CPUs implement new machine instructions (for example, AVX_ or AVX2_ vector instructions) which do not exist on older generations of CPUs.
The general rules of CPU usage are:

* Code compiled on **newer** CPUs may likely crash if executed on **older** CPUs.
* Code compiled on **older** CPUs (older node types) is likely to **run much slower on newer nodes**
  because performance-enhancing vector instructions etc. are not used.
* **Do not run old binaries** compiled on other and older systems (such as CentOS 7 Linux).
  Such binaries will run slowly or may likely crash.

Read more about CPU architectures and instructions here:

* `Instruction set architecture <https://en.wikipedia.org/wiki/Instruction_set_architecture>`_.
* `x86_64 instruction set <https://en.wikipedia.org/wiki/X86-64>`_.

File transfer to and from Niflheim
==================================

If you need to transfer files to and from Niflheim, please use SSH's transfer method `scp <https://en.wikipedia.org/wiki/Secure_copy>`_ (*Secure Copy*).

You can also synchronize directories between Niflheim and your local (CAMD)
machine in a simple way by using `rsync <https://samba.anu.edu.au/rsync/>`_ over an SSH connection.
On your local machine you may find these commands useful::

  From Niflheim to your local machine:
  rsync -av -e ssh sylg.fysik.dtu.dk:niflheim_directory/ local_directory/

  From your local machine to Niflheim:
  rsync -av -e ssh local_directory/ sylg.fysik.dtu.dk:niflheim_directory/

(Note that **trailing ``/`` is important** with ``rsync`` - read the ``rsync`` man-page first).

Another useful option to `rsync` is `--exclude-from=FILE` that allows one to exclude files/directories specified in the file `FILE`.
Note that paths in `FILE` must be relative to the root directory of the source, e.g. `niflheim_directory/` in the first example above.

If the disk on your local machine is formatted as a Windows FAT_/FAT32 filesystem (for example, on an external USB disk) 
we suggest using these flags with *rsync*::

  rsync -rltv --modify-window=1 -e ssh sylg.fysik.dtu.dk:niflheim_directory/ USB-disk/

If the disk on your local machine is formatted as a Windows ExFAT_ filesystem (for example, on an external USB disk) use `these options <https://www.scivision.dev/rsync-to-exfat-drive/>`_::

  rsync -rltv -e ssh sylg.fysik.dtu.dk:niflheim_directory/ USB-disk/

**NOTICE** about ExFAT_ file systems: 

* ExFAT_ file systems do not support the concept of a symbolic_link_ (soft link) file.
* File names **must not** contain ":" or other special characters, see `www.ntfs.com <https://www.ntfs.com/exfat-filename-dentry.htm>`_.
  Such file names may be renamed using the Linux ``rename`` command.

Windows users may use `WinSCP <https://winscp.net/eng/docs/introduction>`_ or `FileZilla <https://filezilla-project.org/>`_, to do ``scp`` or ``sftp`` operations.

.. _FAT: https://en.wikipedia.org/wiki/File_Allocation_Table
.. _ExFAT: https://en.wikipedia.org/wiki/ExFAT
.. _symbolic_link: https://superuser.com/questions/1256530/linux-links-shortcuts-in-exfat-filesystem

Slurm batch queueing system
===========================

Here is a brief introduction to the usage of Slurm_:

* Slurm_tutorials_ from the creators of the software.
* Slurm_Quick_Start_ User Guide.
* Slurm_docs_.
* Command_Summary_ (2-page sheet).
* Slurm_FAQ_.
* `Slurm Quick Start Tutorial <https://www.ceci-hpc.be/slurm_tutorial.html>`_ from CÉCI in Belgium.
* `Transitioning to SLURM from Moab/Torque <https://sites.google.com/a/case.edu/hpc-upgraded-cluster/slurm-cluster-commands>`_.

.. _Slurm: https://www.schedmd.com/
.. _Slurm_tutorials: https://slurm.schedmd.com/tutorials.html
.. _Slurm_Quick_Start: https://slurm.schedmd.com/quickstart.html
.. _Slurm_docs: https://slurm.schedmd.com/
.. _Slurm_FAQ: https://slurm.schedmd.com/faq.html
.. _Command_Summary: https://slurm.schedmd.com/pdfs/summary.pdf

.. _compute_node_partitions:

Compute node partitions
-----------------------

Slurm_ node **partitions** are the compute resource in Slurm_ which group nodes into logical and possibly overlapping sets.

To display the status of all available Slurm_ partitions use the ``showpartitions`` command (append ``-h`` for help).

Niflheim contains a number of node partitions with different types of CPU architecture hardware and the corresponding recommended login nodes:

.. list-table::
  :widths: 4 8 4 4 4 4 4

  * - **Partition**
    - **CPU architecture**
    - **CPU cores**
    - **RAM memory**
    - **/tmp scratch disk**
    - **Login nodes**
    - **Linux OS**
  * - xeon24el8, xeon24el8_test, xeon24el8_week
    - Broadwell_
    - 24
    - 254 GB
    - 140 GB
    - slid2
    - RockyLinux_ 8
  * - xeon40el8
    - Skylake_ and Cascade_Lake_.
    - 40
    - 380 GB
    - 140 GB
    - thul, svol
    - RockyLinux_ 8
  * - xeon40el8_768
    - Skylake_
    - 40
    - 760 GB
    - 140 GB
    - thul, svol
    - RockyLinux_ 8
  * - xeon40el8_clx
    - Cascade_Lake_
    - 40
    - 380 GB
    - 140 GB
    - thul, svol
    - RockyLinux_ 8
  * - sm3090el8
    - Skylake_ + GPUs
    - 80 (40*2 with HT)
    - 192 GB
    - 800 GB
    - thul
    - AlmaLinux_ 8
  * - sm3090el8_768
    - Skylake_ + GPUs
    - 80 (40*2 with HT)
    - 768 GB
    - 800 GB
    - thul
    - AlmaLinux_ 8
  * - xeon56
    - IceLake_
    - 56
    - 512 GB
    - 293 GB
    - surt
    - AlmaLinux_ 8
  * - epyc96
    - AMD EPYC Zen4_ 9474F
    - 96
    - 768 GB
    - 1.7 GB
    - fjorm
    - RockyLinux_ 8
  * - xeon32_4096
    - IceLake_
    - 32
    - 4096 GB
    - 14 TB
    - surt
    - RockyLinux_ 8
  * - a100
    - IceLake_ + 4* A100_ NVLink_ GPUs
    - 128 (16*4 with HT) 
    - 512 GB
    - 1.7 TB
    - surt
    - RockyLinux_ 8

**Please notice** the following points:

* The default **maximum time limit** for jobs is **50 hours** in all partitions.
  However, the ``xeon24_week`` partition will accept jobs up to **1 week** (168 hours).
  The ``xeon24el8_test`` partition has a 10 minute time limit and must be used only for development tests.

* Please use **all CPU cores** in the most modern CPU compute nodes (``xeon40``, ``xeon56``, and ``epyc96`` partitions),
  and do not submit jobs to these partitions which only use partial nodes.

* Partial node usage, including single-core jobs, are permitted in the ``xeon24`` partition by submitting to 1 and up to 23 cores of a 24-core node.

* Partial node jobs are also permitted in the partitions ``xeon32_4096`` (**BIG memory**) as well as the GPU partitions ``sm3090`` and ``a100``.

* Please do not use the GPU partitions ``a100`` or ``sm3090`` unless your group has been authorized to use GPUs.

* The RAM memory is slightly less than the physical RAM due to operating system overheads.

* The ``xeon40`` partition consists of both Skylake_ and Cascade_Lake_ CPU types.
  While these CPUs are (almost) binary compatible, the new Cascade_Lake_ CPUs will have a higher performance.

* Some partitions are overlapping so that nodes with more memory are also members of the partition with the lower amount of memory.

* The **local node scratch disk space** is **shared** between all Slurm_ jobs currently running on the node, see `Using compute node temporary scratch disk space`_ below.

Compute nodes and jobs
----------------------

Use sinfo_ to view available nodes::

  sinfo

and to view the queue use squeue_::

  squeue

and for an individual user ($USER in this example)::

  squeue -u $USER

To see detailed information about a job-id use this command::

  showjob <jobid>

List of pending jobs in the same order considered for scheduling by Slurm::

  squeue --priority --sort=-p,i --states=PD

Hint: Set an environment variable in your ``.bashrc`` file so that the default output format contains more information::

  export SQUEUE_FORMAT="%.18i %.9P %.8j %.8u %.10T %.9Q %.10M %.9l %.6D %.6C %R"

or for even more details::

  export SQUEUE_FORMAT2="JobID:8,Partition:11,QOS:7,Name:10 ,UserName:9,Account:9,State:8,PriorityLong:9,ReasonList: ,TimeUsed:12,SubmitTime,TimeLimit:11,NumNodes:6,NumCPUs:5,MinMemory:6"

To change the time display format see ``man squeue``, for example::

  export SLURM_TIME_FORMAT="%a %T"

To show all jobs on the system with one line per user::

  showuserjobs

Submitting batch jobs to Niflheim
---------------------------------

The command sbatch_ is used to submit jobs to the batch queue.
Submit your Slurm_ script file by::

  sbatch scriptfile

See the above mentioned pages for information about writing Slurm_ script files, which may contain a number batch job parameters.
See the sbatch_ page and this example::

  #!/bin/bash
  #SBATCH --mail-type=ALL
  #SBATCH --mail-user=<Your E-mail>  # The default value is the submitting user.
  #SBATCH --partition=xeon24
  #SBATCH -N 2      # Minimum of 2 nodes
  #SBATCH -n 48     # 24 MPI processes per node, 48 tasks in total, appropriate for xeon24 nodes
  #SBATCH --time=1-02:00:00
  #SBATCH --output=mpi_job_slurm_output.log
  #SBATCH --error=mpi_job_slurm_errors.log

It is **strongly recommended** to specify both nodes and tasks numbers so that jobs will occupy entire nodes (see `Compute node partitions`_).
For selecting the correct number of **nodes** and **tasks** (cores) see the sbatch_ man-page items::

  -N, --nodes=<minnodes[-maxnodes]>    # Request that a minimum of minnodes nodes be allocated to this job. A maximum node count may also be specified with maxnodes. If only one number is specified, this is used as both the minimum and maximum node count...
  -n, --ntasks=<number>                # Number of tasks

You may validate your batch script, and return an estimate of when a job would be scheduled to run::

  sbatch --test-only <scriptfile>  # No job is actually submitted.

You can select a specific node partition (see `Compute node partitions`_) with lines in the script (or on the command line):

* Select the 24-core nodes in the *xeon24 partition*::

  #SBATCH --partition=xeon24

* Select the 24-core nodes in the *xeon24 partition* which also have **512 GB RAM** memory::

  #SBATCH --partition=xeon24_512

.. _sbatch: https://slurm.schedmd.com/sbatch.html
.. _squeue: https://slurm.schedmd.com/squeue.html
.. _sinfo: https://slurm.schedmd.com/sinfo.html
.. _scancel: https://slurm.schedmd.com/scancel.html
.. _scontrol: https://slurm.schedmd.com/scontrol.html


If you have permission to charge jobs to another (non-default) account, jobs can be submitted like::

  sbatch -A <account>

To delete a job use scancel_::

  scancel <jobid>

To hold or release a jobid *xxx* use the scontrol_ command::

  scontrol hold xxx 	Hold a job
  scontrol release xxx 	Release a held job

View status of jobs and nodes
.............................

You can view your jobs (running, pending, etc.) with squeue_ like these examples::

  squeue -u $USER
  squeue -u $USER -t running
  squeue -u $USER -t pending

To get information about the status of the compute nodes running your jobs,
use the pestat_ command::

  pestat -u $USER

The pestat_ lists usage of CPU cores, RAM memory, GPUs (if used), and the current CPU load with 1 line per node.
To see all the possible pestat_ options::

  pestat -h

You may use this information to determine if your jobs are behaving correctly in terms of CPU and memory resources.

.. _pestat: https://github.com/OleHolmNielsen/Slurm_tools/tree/master/pestat

User limits on batch jobs
.........................

It may happen that some jobs will be pending due to limits_ imposed on the user account.
The typical reasons for a job not starting are that the following limits could be exceeded:

* **AssocGrpCpuLimit**: Limit on the number of CPU cores.
* **AssocGrpCPURunMinutesLimit**: Limit on the number of CPU cores multiplied by the minutes of wallclock time requested.
* **AssocGrpNodeLimit**: Limit on the number of compute nodes.
* **MaxJobsAccrue**: Maximum number of pending jobs able to accrue age priority

For a full list of limits, see the section `Limits in both Associations and QOS <https://slurm.schedmd.com/resource_limits.html#limits>`_ in the limits_ page.

Use the following command to display the limits currently in effect for your userid::

  showuserlimits

Use ``showuserlimits -h`` to see all options.
For example, to display the number of CPUs limit::

  showuserlimits -l GrpTRES -s cpu

Newly created users will have some lower limits for the first 30 days in order to prevent erroneous bad usage of the system.

.. _limits: https://slurm.schedmd.com/resource_limits.html

Fairshare usage
...............

We have defined the following Slurm_ FairShare_ default parameters:

.. list-table::
  :widths: 4 4

  * - **User type**
    - **FairShare**

  * - VIP/PhD
    - 3%
  * - Student
    - 2%
  * - Faculty
    - 5%
  * - Guest/external
    - 1%

To display job FairShare_ priority values use::

  sprio -l -u $USER

.. _FairShare: https://slurm.schedmd.com/priority_multifactor.html#fairshare

Job arrays
..........

Job_arrays_ offer a mechanism for submitting and managing collections of similar jobs quickly and easily; job arrays with millions of tasks can be submitted in milliseconds (subject to configured size limits). 
All jobs must have the same initial options (e.g. size, time limit, etc.), however it is possible to change some of these options after the job has begun execution using the scontrol command specifying the JobID of the array or individual ArrayJobID.

Job_arrays_ are only supported for batch jobs and the array index values are specified using the --array or -a option of the sbatch command. 
The option argument can be specific array index values, a range of index values, and an optional step size as shown in the examples below. 

Jobs which are part of a job array will have the environment variable SLURM_ARRAY_TASK_ID set to its array index value.

See some examples of usage in the Job_arrays_ page.


.. _Job_arrays: https://slurm.schedmd.com/job_array.html

Using compute node temporary scratch disk space
...............................................

It is very important that every user **refrain from overloading the central file servers**!
This may happen when jobs write job temporary files to their ``$HOME`` directories on those file servers.

Users are kindly requested to configure job scripts to use the compute nodes' **/tmp** folder for any temporary files in the job.
This may sometimes be implemented by using this job script command::

  export TMPDIR=/tmp

This ``$TMPDIR`` setting is the default value in many computer codes and may not need to be set explicitly.

Notes:

* On the **login nodes** you **must not** use ``/tmp`` for large files!
  Please use in stead the local ``/scratch/$USER`` folder.

Technical details:

* Each Slurm_ job automatically allocates a **temporary /tmp** disk space which is private to the job in question.
* This temporary disk space lives only for the duration of the Slurm_ job, and is automatically deleted when the job terminates.
* This temporary disk space is actually allocated on the compute node's local ``/scratch`` disk, the size of which is specified above under the *Compute node partitions* section.

Shared scratch disk spaces
..........................

For those applications which require the medium-term use of scratch files across several different nodes or for subsequent batch jobs,
we provide some scratch file spaces at::

  /home/scratch3/$USER/         # CAMD, CatTheory, Energy groups
  /home/scratch11/$USER/        # Construct/MEK group

**REMEMBER:** There is **no backup** of files!!
Lost files cannot be recovered by any means!

Please remember to clean up scratch files regularly when they are no longer needed.

Viewing completed or failed job information
--------------------------------------------

After your job has completed (or terminated), you can view job accounting data by inquiring the Slurm_ database.
For example, to inquire about a specific job Id 1234::

  sacct -j 1234 -o jobid,jobname,user,Timelimit,Elapsed,NNodes,Partition,ExitCode,nodelist

If some jobs have failed or been cancelled, you can display a list of such jobs within a given time interval using a command such as::

  sacct -s FAILED,CANCELLED -X --starttime 2024-01-11T19:00 --endtime 2024-01-12T09:00 -o User,jobid,jobname%40,partition,State,ExitCode

Here the ``--starttime`` indicates the *Start* and ``--endtime`` indicates the *End* of the desired time interval.
The ``sacct`` manual page documents the *valid time formats*.

You may inquire about many job parameters, to see a complete list run::

  sacct -e

Correct usage of node types
============================

Usage of multi-CPU nodes
-------------------------

The most modern compute nodes with many CPU cores should be utilized fully by the batch jobs::

  epyc96 node jobs should utilize 96 CPU cores per node
  xeon56 node jobs should utilize 56 CPU cores per node
  xeon40 node jobs should utilize 40 CPU cores per node

If you have jobs that utilize **less than 40 CPU cores per node**, we request that you use the older compute nodes::

  xeon24 nodes permit jobs using 1-24 CPU cores on 1 node
  xeon24 node jobs should utilize 24 CPU cores per node, but only in case 2 or more nodes are requested

Please see also the list of `Compute node partitions`_.

Job scripts that do not use CPU cores or GPUs correctly may be rejected at submit time or be cancelled by the administrators.

Usage of BIG memory nodes
-------------------------

We have installed 4 **BIG memory** nodes for special applications used by selected groups.
These nodes have 4096 GB (4 TB) of RAM memory,
and it is expected (required) that all jobs submitted to the ``xeon32_4096`` partition will use **at least 768 GB** of RAM memory
and/or use the large scratch disk space.
Jobs using up to 768 GB of RAM memory should use one of the other `Compute node partitions`_.
Partial-node jobs are permitted in the ``xeon32_4096`` partition.

The ``xeon32_4096`` nodes are also equipped with a very large (14 TB) and very fast scratch file system.
Large scratch spaces are typically required by big-memory jobs.
Slurm_ jobs use the local scratch disk as the job's private ``/tmp`` directory,
but note that the scratch disk space is shared between all jobs on the node. 

Here are some special instructions for submitting jobs to the ``xeon32_4096`` partition:

- Memory must **always** be specified in the Slurm_ submit script.
  Memory can be specified in either of two ways: ``--mem=xx`` for the total memory requirement of the job or ``--mem-per-cpu=xx`` for memory per CPU allocated in the job.
- Any job can ask for up to 4 TB of memory even if it does not require all of the CPU cores, for example::

    #SBATCH --mem=3000GB
    #SBATCH -n 4

  Here, Slurm_ will allocate 4 cores and 3 TB of memory.
  This means that another job can run on the same node utilizing at most the remaining 28 cores and 1 TB of memory.

Job scripts that do not use CPU cores correctly may be rejected at submit time or be cancelled by the administrators.

Usage of GPU compute nodes
--------------------------

Please do not use the GPU partitions unless your group has been authorized to use GPUs.
The appropriate login nodes (RockyLinux_ / AlmaLinux_ 8) for GPU partitions are:

* Partition ``sm3090``: **thul** (Skylake_)
* Partition ``a100``: **surt** (IceLake_)

The appropriate login node must be used to build software for GPUs, since they have the same CPU architecture as the GPU-nodes.
GPU-specific software modules will only be provided on GPU-compatible nodes.

NVIDIA's CUDA_ software is available as a module on the login nodes and compute nodes::

  $ module avail CUDA/

Batch jobs submitted to the GPU nodes **must request GPU resources**!  
Jobs that only use CPUs without using GPUs are **not permitted**.
Partial node jobs are permitted in the GPU partitions.

You must include batch job statements for specifying correct numbers of CPUs and GPUs.
Since the nodes in the ``sm3090`` partition have 10 GPUs each and 80 "virtual" CPU cores, 
you **must** submit jobs with 80/10 = **8 CPUs per GPU**::

  #SBATCH -n 8

For example, to submit a batch jobs to 1 GPU on 8 CPU cores of a node in the ``sm3090`` partition::

  #SBATCH --partition=sm3090
  #SBATCH -N 1-1
  #SBATCH -n 8
  #SBATCH --gres=gpu:1

Similarly, the nodes in the ``a100`` partition have 4 A100_ GPUs each and 128 "virtual" CPU cores,
so you should request 32 CPU cores per GPU.
Job scripts that do not use CPU cores or GPUs correctly may be rejected at submit time or be cancelled by the administrators.

For further Slurm_ information see the GRES_ page.

.. _CUDA: https://en.wikipedia.org/wiki/CUDA
.. _Tesla: https://www.nvidia.com/object/tesla-servers.html
.. _GRES: https://slurm.schedmd.com/gres.html

Software environment modules
============================

The classical problem of maintaining multiple versions of software packages and compilers is solved using Software_Modules_.

.. _Software_Modules: https://en.wikipedia.org/wiki/Environment_Modules_%28software%29

Niflheim uses the Lmod_ implementation of software environment modules (we do not use the *modules* command which might be supplied by the OS).
For creating modules we support the EasyBuild_modules_ build and installation framework.

The Lmod_ command ``module`` (and its brief equivalent form ``ml``) is installed on all nodes.

Read the Lmod_User_Guide_ to learn about usage of modules.
For example, to list available modules::

  module avail
  ml av

You can load any available module like in this example::

  module load GCC
  ml GCC

If you work on different CPU architectures, it may be convenient to turm off Lmod_'s caching feature by::

  export LMOD_IGNORE_CACHE=1

**WARNING:**  With a software module system there is an important advice::

  Do NOT modify manually the environment variable LD_LIBRARY_PATH

.. _Lmod_User_Guide: https://www.tacc.utexas.edu/research-development/tacc-projects/lmod/user-guide


Loading complete toolchains
---------------------------

The modules framework at the :ref:`niflheim` includes a number of convenient toolchains_ built as EasyBuild_modules_.
We currently provide these toolchains_:

* The intel toolchain provides Intel_compilers_ (Parallel Studio XE), the Intel MKL_ Math Kernel library, and the Intel_MPI_ message-passing library.

  Usage and list of contents::

    module load intel
    module list

* The foss toolchain provides **GCC, OpenMPI, OpenBLAS/LAPACK, ScaLAPACK(/BLACS), FFTW**.

  Usage and list of contents::

    module load foss
    module list

* The iomkl toolchain provides Intel_compilers_, Intel MKL_, **OpenMPI**.

  Usage and list of contents::

    module load iomkl
    module list

In the future there may be several versions of each toolchain, list them like this::

  module whatis foss
  module whatis iomkl

.. _toolchains: https://easybuild.readthedocs.io/en/latest/eb_list_toolchains.html
.. _Intel_MPI: https://software.intel.com/en-us/mpi-library
.. _Intel_compilers: https://software.intel.com/en-us/parallel-studio-xe

Some notes about modules
------------------------

Matplotlib
..........

Matplotlib_ has a term called a Matplotlib_backend_ and you can specify it by::

  export MPLBACKEND=module://my_backend 

If Matplotlib_ cannot start up, in some cases you have to turn the Matplotlib_backend_ off by::

  unset MPLBACKEND

.. _Matplotlib: https://matplotlib.org/
.. _Matplotlib_backend: https://matplotlib.org/tutorials/introductory/usage.html#backends

Intel VTune Profiler
....................

We have installed module for the Intel VTune_ Profiler::

  module load VTune

Please read the VTune_documentation_.

.. _VTune: https://software.intel.com/en-us/vtune
.. _VTune_documentation: https://software.intel.com/en-us/vtune/documentation/featured-documentation

Need additional modules?
------------------------

Please send your requests for additional modules to the :ref:`Niflheim_support` E-mail. 
We will see if EasyBuild_modules_ are already available.

Building your own modules
-------------------------

It is possible for you to use your personal modules in addition to those provided by the :ref:`niflheim` system.
If you use EasyBuild_modules_ you can define your private module directory in your home directory and prepend it to the already defined modules::

  mkdir $HOME/modules
  export EASYBUILD_PREFIX=$HOME/modules
  module use $EASYBUILD_PREFIX/modules/all
  module load EasyBuild

and then build and install EasyBuild_modules_ into ``$HOME/modules``.
If you need help with this, please write to the :ref:`Niflheim_support` E-mail. 

.. _Environment_modules: https://modules.sourceforge.net/
.. _Lmod: https://www.tacc.utexas.edu/research-development/tacc-projects/lmod 

Please note that the :ref:`niflheim` is a heterogeneous cluster comprising several generations of CPUs,
where the newer ones have CPU instructions which don't exist on older CPUs.
Therefore code compiled on a new CPU may crash if executed on an older CPU.
However, the Intel_compilers_ should generate multiple versions of machine code which may automatically select the correct code at run-time.

If you compile code for the "native" CPU-architecture, it is proposed that you compile separate versions for each CPU architecture.
For your convenience we offer a system environment variable which you may use to select the correct CPU architecture::

  [ohni@svol ~]$ echo $CPU_ARCH
  skylake

The Skylake_ architecture corresponds to the *xeon40* compute nodes, and the GCC compiler (version 4.9 and above) will recognize this architecture name::

  module load GCC
  gcc -march=native -Q --help=target | grep march | awk '{print $2}'
  skylake

GPAW and ASE software on Niflheim
=================================

Prebuilt software modules for GPAW_ and ASE_ are available on Niflheim.
List the modules by::

  $ module avail GPAW/ ASE/ 

It is recommended to read the instructions in https://wiki.fysik.dtu.dk/gpaw/platforms/platforms.html for different ways to use GPAW and ASE on Niflheim.

.. _GPAW: https://wiki.fysik.dtu.dk/gpaw
.. _ASE: https://wiki.fysik.dtu.dk/ase

Jupyter Notebooks on Niflheim
=============================

Jupyter_Notebook_ documents are documents produced by the *Jupyter Notebook App*, which contain both computer code (e.g. python) and rich text elements (paragraph, equations, figures, links, etc…). 
Notebook documents are both human-readable documents containing the analysis description and the results (figures, tables, etc..) as well as executable documents which can be run to perform data analysis.

On Niflheim we have installed Jupyter_Notebook_ software modules which you can load and use::

  $ module avail JupyterNotebook
  -------------------------- /home/modules/modules/all ---------------------------
   JupyterNotebook/7.0.2-GCCcore-12.3.0

You have to select the correct *jupyter* version shown above, according to which compiler has been used to compile the other software you are using (such as GPAW).

**NOTE:** If you use a *Python virtual environment* (venv_), you cannot use the *IPython* module, as the Jupyter_Notebook_ will not see the modules in the venv_. 
Instead you have to install jupyter in your venv_ (``pip install notebook``).

.. _venv: https://docs.python.org/3/library/venv.html

Restrictions on the use of Jupyter Notebook
-------------------------------------------

*  **NOTICE: Jupyter Notebooks cannot be connected to directly from any other network at DTU or outside DTU.**

* The web-server on port 8888 can only be accessed from a PC on the Fysik cabled network (including *demon*).

* The ``jupyter`` command starts a special web-server on the login node serving a network port number 8888 (plus/minus a small number).

Using Jupyter_Notebook_ documents on Niflheim from DTU Physics
--------------------------------------------------------------

1. Log in to a Niflheim login node, preferably *slid*.

2. Load the relevant module, for example::

     module load JupyterNotebook

   Users of venv_ should **not** load this module!

3. Go to the relevant folder for your notebooks, and start Jupyter with the command::

      jupyter notebook --no-browser --ip=$HOSTNAME

   Jupyter will respond with around ten lines of text, at the bottom is a URL.  
   Paste that URL into a browser on your local machine.

4. **IMPORTANT:** Once you are done using your notebooks, **remember to shut down the Jupyter server** so you do not tie up valuable ressources (mainly RAM and port numbers).

   You shut down *Jupyter* by either:

   a. Pressing **Control-C twice** in the terminal running the `jupyter` command, *or*
   b. Clicking on the **Quit button** on the Jupyter overview page

      This is **not** the same as the ``Logout`` buttons on each notebook, which will disconnect your browser from the Jupyter server, but actually leave Jupyter running on the login node.

Using Jupyter_Notebook_ documents on Niflheim from home/elsewhere (Linux or Mac)
--------------------------------------------------------------------------------

Use these instructions when you are located outside DTU Physics, and your laptop/desktop
is running Linux or MacOS.

1. Connect to the DTU_VPN_
   
2. Log in to a Niflheim login node, preferably *slid*.

3. Load the relevant module, for example::

     module load JupyterNotebook/7.0.2-GCCcore-12.3.0

   Users of venv_ should **not** load this module!

4. Go to the relevant folder for your notebooks, and start Jupyter with the command::

      jupyter notebook --no-browser

   Jupyter will respond with around ten lines of text, at the bottom is a URL.  
   It will contain the text ``localhost:NNNN`` where NNNN is a port number, typically 8888 or close.  You need that number in the next step.

5. From your desktop/laptop, log in to niflheim again in a new window, using this command to set up an SSH tunnel::
      
      ssh -L NNNN:localhost:NNNN username@xxxx.fysik.dtu.dk -N

   where:

   * ``xxxx`` is slid or whatever machine you are using,
   * ``username`` is your DTU username,
   * ``NNNN`` is the port number printed by the notebook command,
     
   *Note* There will be no output from this command. To test if it is working; proceed to the next step.

6. Open a browser, and cut-and-paste the address starting with ``https://localhost`` into your browser.

7. **IMPORTANT:** Once you are done using your notebooks, **remember to shut down the Jupyter server!** See point 4 
   in the instructions in the previous section (usage from DTU Physics).

.. _Jupyter_Notebook: https://jupyter-notebook-beginner-guide.readthedocs.io/en/latest/what_is_jupyter.html
.. _DTU_VPN: https://www.inside.dtu.dk/en/medarbejder/it-og-telefoni/it-systemer-og-retningslinjer/it-systemer-og-vaerktoejer/it-systemer-ait/vpn

Using Jupyter_Notebook_ documents on Niflheim from home/elsewhere (Windows)
---------------------------------------------------------------------------

Use these instructions when you are located outside DTU Physics, and your laptop/desktop
is running Microsoft Windows.

1. Log in to a Niflheim login node, preferably *slid*.
   Use MobaXterm_ to log in directly to e.g. slid.fysik.dtu.dk, but when you create the login session (the Session tab), select Network Settings, then Jump Host.  Fill in the Jump Host (and your DTU user name).

2. Load the relevant module, for example::

     module load JupyterNotebook/7.0.2-GCCcore-12.3.0

   Users of venv_ should **not** load this module!

3. Go to the relevant folder for your notebooks, and start Jupyter with the command::

      jupyter notebook --no-browser --ip=$HOSTNAME

   Note the extra ``--ip`` option needed when connecting with MobaXterm_. 
   Jupyter will respond with around ten lines of text, at the bottom is a URL.  
   It will contain the text ``localhost:NNNN`` or ``127.0.0.1:NNNN`` where NNNN is a port number, typically 8888 or close.  You need that number in the next step.

4. Use MobaXterm_ to set up an SSH tunnel (the Tunneling tab).
   
   * On "My computer" enter **port number printed by jupyter**.

   * On "SSH server", enter the jump host hostname, and your DTU username as SSH user.  Leave the port number blank.

   * On the remote server, enter "slid.fysik.dtu.dk" (or whatever node you are using) as the Remote server name, and the **port number printed by jupyter** as the port number.

   Click save, and then start the tunnel with the small "play" icon.

5. Open a browser, and cut-and-paste the address starting with ``https://localhost`` or ``http://127.0.0.1`` into your browser.

6. **IMPORTANT:** Once you are done using your notebooks, **remember to shut down the Jupyter server!** See point 4 
   in the instructions in the previous section (usage from DTU Physics).

Containers on Niflheim
======================

Containers_ for virtual operating system and software environments have become immensely popular.
The most well-known Containers_ system is Docker_, and huge numbers of Containers_ have been created for this environment.
Containers_ are well suited to running one or two applications non-interactively in their own custom environments.
Containers_ share the under-lying Linux kernel of the host system, so only Linux Containers_ can exist on a Linux host.

However, Docker_ is not well suited for a shared multi-user system, let alone an HPC supercomputer system, primarily due to security issues and performance issues with parallel HPC applications.
Please see the Apptainer_security_ page.

A Containers_ technology created for HPC is Apptainer_ (previously known as Singularity_).
Apptainer_ assumes (more or less) that each application will have its own container. 
Apptainer_ assumes that you will have a build system where you are the root user, but that you will also have a production system where you may not be the root user.

Please consult the Apptainer_documentation_ for further information.
There is a *Singularity video tutorial* on the Apptainer_ homepage.
For system administrators there are some useful pages
`Admin Quick Start <https://docs.sylabs.io/guides/latest/admin-guide/admin_quickstart.html>`_
and
`User Namespaces & Fakeroot <https://docs.sylabs.io/guides/latest/admin-guide/user_namespace.html>`_.

.. _Containers: https://cloud.google.com/containers/
.. _Docker: https://www.docker.com/
.. _Apptainer: https://apptainer.org/
.. _Apptainer_security: https://apptainer.org/docs/user/main/security.html
.. _Apptainer_documentation: https://apptainer.org/docs/user/latest/
.. _Singularity: https://en.wikipedia.org/wiki/Singularity_(software)

Apptainer on Niflheim
-----------------------

We have installed Apptainer_ (current version: 1.3 from EPEL) as RPM packages.

If you have root priviledge on your personal Linux PC, you may want to make an Apptainer_ installation locally on the PC.
Finished containers can be copied to Niflheim, and executing Apptainer_ containers is as a **normal user** without any root priviledge at all!

Please note that you must build containers within a **local file system** (not a shared file system like NFS where root access is prohibited).

Docker_ containers can be executed under Apptainer_.
For example, make a test run of a simple Docker_ container from DockerHub_::

  apptainer run docker://godlovedc/lolcow

You can run many recent versions of CentOS_ Docker_ containers from the `CentOS library <https://hub.docker.com/r/library/centos/>`_, for example a 6.9 container::

  apptainer run docker://centos:centos6.9

Ubuntu Linux may be run from the `Ubuntu library <https://hub.docker.com/_/ubuntu/>`_::

  apptainer run docker://ubuntu:17.10

Application codes may also be on DockerHub_, for example an `OpenFOAM container <https://hub.docker.com/r/openfoam/>`_ can be run with::

  apptainer run docker://openfoam/openfoam4-paraview50 

.. _DockerHub: https://hub.docker.com/explore/

Apptainer batch jobs
----------------------

You can submit normal Slurm_ batch jobs to the queue running Apptainer_ containers just like any other executable.

An example job script running a container image ``lolcow.simg``::

  #!/bin/sh
  #SBATCH --mail-type=ALL
  #SBATCH --partition=xeon24
  #SBATCH --time=05:00
  #SBATCH --output=lolcow.%J.log
  apptainer exec lolcow.simg cowsay 'How did you get out of the container?'

To run a Apptainer_ container in parallel on 2 nodes and 10 CPU cores with MPI use the following lines::

  #SBATCH -N 2-2
  #SBATCH -n 10
  module load OpenMPI
  mpirun -n $SLURM_NTASKS apptainer exec lolcow.simg cowsay 'How did you get out of the container?'

Visual Studio Code
=====================

The *Visual Studio Code* (VS_code_) editor can be used on your personal desktop and make remote SSH connections to the Niflheim login nodes.

The DTU `course 02002/02003: Computer Programming <https://02002.compute.dtu.dk/index.html>`_
has some material in the page `Using VSCode <https://02002.compute.dtu.dk/vscode/index.html>`_.

There is a bug with remote SSH connections from VS_code_ which will leave processes behind on the remote server,
even after you quit VS_code_, see VS_code_bug_8546_.
The workaround is to add to your VS_code_ file ``settings.json`` the line::

  "remote.SSH.useLocalServer": true 

Enabling ``useLocalServer`` will be the default in the future, but hasn't happened yet due to some issues on Windows SSH servers.

The Settings_editor_ is the UI that lets you review and modify setting values that are stored in a ``settings.json`` file. 
The location is documented in `Settings file locations <https://code.visualstudio.com/docs/getstarted/settings#_settings-file-locations>`_.

.. _VS_code: https://code.visualstudio.com/
.. _VS_code_bug_8546: https://github.com/microsoft/vscode-remote-release/issues/8546
.. _Settings_editor: https://code.visualstudio.com/docs/getstarted/settings#_settingsjson

Pages for system administrators
===============================

* `Slurm batch queueing system <https://wiki.fysik.dtu.dk/Niflheim_system/SLURM>`_.
* `Cornelis Networks Omni-Path network fabric <https://wiki.fysik.dtu.dk/Niflheim_system/OmniPath>`_.
* `EasyBuild software for environment modules on the RHEL Linux family <https://wiki.fysik.dtu.dk/Niflheim_system/EasyBuild_modules>`_.
