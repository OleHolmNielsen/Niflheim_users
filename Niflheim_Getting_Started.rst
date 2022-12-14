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
* CentOS_ 7 and AlmaLinux_ 8 Linux OS,
* Slurm_ batch queueing system,
* Software_Modules_ using Lmod_ and EasyBuild_modules_.

Support: Please see the :ref:`Niflheim_support` page.

Accounting: You can see the Niflheim monthly and weekly usage
`accounting reports <https://wiki.fysik.dtu.dk/graphs/accounting_reports.html>`_.

.. _CentOS: https://www.centos.org/
.. _AlmaLinux: https://almalinux.org/
.. _Slurm: https://www.schedmd.com/
.. _EasyBuild_modules: https://wiki.fysik.dtu.dk/Niflheim_system/EasyBuild_modules/

Login to Niflheim
=================

See the :ref:`Access_to_Niflheim` section about getting access.
Login to *Niflheim* is available with SSH_ **only** from within the DTU network.
If you are outside of DTU, please log in to the DTU_VPN_ service or the G-databar_ first.

.. _DTU_VPN: https://www.inside.dtu.dk/en/medarbejder/it-og-telefoni/wifi-og-fjernadgang/vpn-cisco-anyconnect/vpn-ait-dtu-dk
.. _G-databar: https://www.gbar.dtu.dk/

Please login to the node type identical to the compute-nodes onto which you submit batch jobs.
See `Compute node partitions`_ below and the :ref:`Hardware` page.

The login nodes  are:

* **thul.fysik.dtu.dk**: Login node for CPU type **xeon16**:

  * thul: A 16-CPU (dual-processor, 8-core + Hyperthreading_ = 32 "virtual" cores), 64 GB of RAM.
  * OS: CentOS_ 7.
  * CPUs: Intel(R) Xeon(R) CPU E5-2670 0 @ 2.60GHz Sandy_Bridge_.
  * Refer to this as CPU_ARCH= **sandybridge**. Use this login node also for the **ivybridge** architecture.

* **sylg.fysik.dtu.dk** and **slid.fysik.dtu.dk**: Login nodes for CPU type **xeon24**:

  * The Intel CPU type **xeon24**.
  * OS: CentOS_ 7.
  * Please build all applications for xeon24 with the latest Intel MKL_ math library (see `Software environment modules`_ below)!
  * A 24-CPU (dual-processor, 12-cores + Hyperthreading_ = 48 "virtual" cores), 256 GB of RAM.
  * CPUs: Intel(R) Xeon(R) CPU E5-2650 v4 @ 2.20GHz Broadwell_
  * Refer to this as CPU_ARCH= **broadwell**.

* **svol.fysik.dtu.dk**: Login node for CPU type **xeon40**:

  * The Intel CPU type **xeon40**.
  * OS: CentOS_ 7.
  * Please build all applications for xeon40 with the latest Intel MKL_ math library (see `Software environment modules`_ below)!
  * A 40-CPU (dual-processor, 20 cores + Hyperthreading_ = 80 "virtual" cores), 768 GB of RAM.
  * CPUs: Intel(R) Xeon(R) Scalable Gold CPU 6148 @ 2.20GHz Skylake_ with AVX512_ vector instructions.
  * Refer to this as CPU_ARCH= **skylake**.

* **surt.fysik.dtu.dk**: Login node for CPU type **xeon56**:

  * The Intel CPU type **xeon56**.
  * OS: AlmaLinux_ 8.
  * Please build all applications for xeon56 with the latest Intel MKL_ math library (see `Software environment modules`_ below)!
  * A 56-CPU (dual-processor, 56 cores + Hyperthreading_ = 112 "virtual" cores), 512 GB of RAM.
  * CPUs: Intel(R) Xeon(R) Gold 6348 CPU @ 2.60GHz IceLake_ with AVX512_ vector instructions.
  * Refer to this as CPU_ARCH= **icelake**.

The login nodes **surt**, **svol**, **sylg**, and **thul** must not be overloaded with heavy tasks, since this will disturb other users.

The login node **slid** would be acceptable for more heavy testing of codes, but please bear in mind that the login nodes may be shared by many users, and no single user should monopolize any login nodes.
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
.. _Sandy_Bridge: https://en.wikipedia.org/wiki/Sandy_Bridge_(microarchitecture)
.. _Ivy_Bridge: https://en.wikipedia.org/wiki/Ivy_Bridge_(microarchitecture)

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

The SSH **public key** from your PC can be appended to the file ``$HOME/.ssh/authorized_keys`` to enable password-less logins.

**WARNING:** DO NOT copy SSH keys from Niflheim to any external computer (for example, your PC) for reasons of security!
The Niflheim SSH keys must only be used on the Niflheim system.

**Optional**: You may create SSH keys using this command on any Niflheim login node::

  authorized_keys

The SSH_ key files will be created in the directory ``$HOME/.ssh/``.
This can be necessary if you use commercial MPI libraries which use SSH in stead of the recommended Slurm_ method for starting tasks.

.. _PuTTY: https://www.chiark.greenend.org.uk/~sgtatham/putty/

Home directory and disk quota
=============================

Every user has a personal home directory on one of the Niflheim file servers, located in a file system allocated to the research group (for example, ``/home/energy/``).

The home directory file servers have a **daily backup** of all changed files.
To request a manual restore of lost files, please send mail to the address in the :ref:`Niflheim_support` page.

To view your current disk quota::

  quota -s

To view file systems mounted on the node (omitting temporary file systems)::

  df -Phx tmpfs

To count your files and their sizes, the login nodes have a nice Python tool cfas_::

  cfas $HOME

.. _cfas: https://github.com/runefriborg/cfas

Usage of binary compiled code
=============================

Users of Niflheim should be aware of some important facts about different CPU types.

Newer CPUs use new machine instructions (especially AVX_ or AVX2_ vector instructions) which do not exist on older CPUs, so:

* Code compiled on **newer** CPUs may potentially crash when executed on **older** nodes.
* Code compiled on **older** CPUs is likely to run much slower on **newer** nodes because available vector instructions are not used.
* **Do not run old binaries** compiled on other and older systems (such as the old Niflheim). Such binaries will run slowly or even crash.

Read more here:

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
  rsync -av -e ssh local_directory/ thul.fysik.dtu.dk:niflheim_directory/

(Note that **trailing ``/`` is important** with ``rsync`` - read the ``rsync`` man-page first).

Another useful option to `rsync` is `--exclude-from=FILE` that allows one to exclude files/directories specified in the file `FILE`.
Note that paths in `FILE` must be relative to the root directory of the source, e.g. `niflheim_directory/` in the first example above.

If the disk on your local machine is formatted as a Windows FAT_/FAT32 filesystem (for example, on an external USB disk) 
we suggest using these flags with *rsync*::

  rsync -rlptv --modify-window=1 -e ssh thul.fysik.dtu.dk:niflheim_directory/ USB-disk/

If the disk on your local machine is formatted as a Windows ExFAT_ filesystem (for example, on an external USB disk) use `these options <https://www.scivision.dev/rsync-to-exfat-drive/>`_::

  rsync -vrltD -e ssh thul.fysik.dtu.dk:niflheim_directory/ USB-disk/

**NOTICE** about ExFAT_ file systems: 

* ExFAT_ file systems do not support the concept of a symbolic_link_ (soft link) file.
* File names **must not** contain ":" or other special characters, see `ntfs.com <https://ntfs.com/exfat-filename-dentry.htm>`_.
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
* `Slurm Quick Start Tutorial <https://www.ceci-hpc.be/slurm_tutorial.html>`_ from C??CI in Belgium.
* `Transitioning to SLURM from Moab/Torque <https://sites.google.com/a/case.edu/hpc-upgraded-cluster/slurm-cluster-commands>`_.

.. _Slurm: https://www.schedmd.com/
.. _Slurm_tutorials: https://slurm.schedmd.com/tutorials.html
.. _Slurm_Quick_Start: https://slurm.schedmd.com/quickstart.html
.. _Slurm_docs: https://slurm.schedmd.com/
.. _Slurm_FAQ: https://slurm.schedmd.com/faq.html
.. _Command_Summary: https://slurm.schedmd.com/pdfs/summary.pdf

Compute node partitions
-----------------------

Slurm_ node **partitions** are the compute resource in Slurm_ which group nodes into logical (possibly overlapping) sets.

To display the status of all available Slurm partitions use the command (append -h for help)::

  showpartitions

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
  * - xeon16
    - Ivy_Bridge_

      Includes also larger memory nodes below.
    - 16
    - 63 GB
    - 198 GB
    - thul
    - CentOS 7
  * - xeon16_128
    - Ivy_Bridge_

      Includes also larger memory nodes below.
    - 16
    - 127 GB
    - 198 GB
    - thul
    - CentOS 7
  * - xeon16_256
    - Ivy_Bridge_
    - 16
    - 254 GB
    - 198 GB
    - thul
    - CentOS 7
  * - xeon24
    - Broadwell_

      Includes also larger memory nodes below.
    - 24
    - 254 GB
    - 140 GB
    - sylg
    - CentOS 7
  * - xeon24_512
    - Broadwell_
    - 24
    - 510 GB
    - 140 GB
    - sylg
    - CentOS 7
  * - xeon40
    - Skylake_ and Cascade_Lake_.

      Includes nodes from

      xeon40_768 and xeon40_clx.
    - 40
    - 380 GB
    - 140 GB
    - svol
    - CentOS 7
  * - xeon40_768
    - Skylake_
    - 40
    - 760 GB
    - 140 GB
    - svol
    - CentOS 7
  * - xeon40_clx
    - Cascade_Lake_
    - 40
    - 380 GB
    - 140 GB
    - svol
    - CentOS 7
  * - sm3090
    - Skylake_ + GPUs
    - 80 (40*2 with HT)
    - 192 GB
    - 800 GB
    - svol
    - CentOS 7
  * - sm3090_768
    - Skylake_ + GPUs
    - 80 (40*2 with HT)
    - 768 GB
    - 800 GB
    - svol
    - CentOS 7
  * - xeon56
    - IceLake_
    - 56
    - 512 GB
    - 293 GB
    - surt
    - AlmaLinux 8

**Please notice** the following points:

* The default **time limit** for jobs is **50 hours**.
  However, the xeon16, xeon16_128, xeon16_256 partitions will accept jobs up to **1 week** (168 hours).

* Please use the most modern compute nodes in the xeon40 and xeon24 partitions fully.'
  Please do not submit jobs to these partitions which only use partial nodes.

* Please do not use the GPU partition ``sm3090`` unless you have been authorized to use GPUs.

* The RAM memory is slightly less than the physical RAM due to operating system overheads.

* The **xeon40** partition consists of both Skylake_ and Cascade_Lake_ CPU types.
  While these CPUs are (almost) binary compatible, the new Cascade_Lake_ CPUs will have a higher performance.

* Partitions are overlapping so that nodes with more memory are also members of the partition with the least memory.

* The local node scratch disk space is shared between Slurm_ jobs currently running on the node, see `Using compute node temporary scratch disk space`_ below.

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

* Select the 16-core nodes in the *xeon16 partition* (The Default partition)::

  #SBATCH --partition=xeon16

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

Correct usage of multi-CPU nodes
................................

The most modern compute nodes with many CPU cores should be used fully by the batch jobs::

  xeon56 nodes should utilize 56 CPU cores per node
  xeon40 nodes should utilize 40 CPU cores per node
  xeon24 nodes should utilize 24 CPU cores per node
  xeon16 nodes should utilize 16 CPU cores per node, in case 2 or more nodes are used

If you have jobs that use **less than 24 CPU cores per node**, we request that you use the older compute nodes::

  xeon16 nodes support jobs using 1-16 CPU cores on 1 node

Please see also the list of `Compute node partitions`_ above.

For correct usage of GPU nodes please see `GPU compute nodes`_ below.

Job scripts the do not use CPU cores or GPUs correctly may be rejected at submit time with an error message.

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

* Previously (before July 7, 2022) we advised to use the ``/scratch/$USER`` folder,
  but we have implemented a new method so that users are now advised to use ``/tmp`` in stead.
* On the **login nodes** you should **not** use ``/tmp`` for large files!
  Please continue to use the ``/scratch/$USER`` folder.

Technical details:

* Each Slurm_ job automatically allocates a **temporary /tmp** disk space which is private to the job in question.
* This temporary disk space lives only for the duration of the Slurm_ job, and is automatically deleted when the job terminates.
* This temporary disk space is actually allocated on the compute node's local ``/scratch`` disk, the size of which is specified above under the *Compute node partitions* section.

Shared scratch disk space
.........................

For those applications which require the use of scratch files across several different nodes, we have a special scratch file space for each user at::

  /home/scratch2/$USER/

This disk space is on an old server with a reasonable but not very high performance.
There is **no backup** of files!!

**Files older than 30 days** will get deleted automatically.

Viewing completed job information
---------------------------------

After your job has completed (or terminated), you can view job accounting data by inquiring the Slurm database.
For example, to inquire about a specific job Id 1234::

  sacct -j 1234 -o jobid,jobname,user,Timelimit,Elapsed,NNodes,Partition,ExitCode,nodelist

You may inquire about many job parameters, to see a complete list run::

  sacct -e

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

GPU compute nodes
=================

The **svol** login node must be used to build software for GPUs, since it has the same CPU architecture as the GPU-nodes,
and since GPU-specific software modules will only be provided on compatible nodes.

CUDA_ software is **only** available as a module on the ``xeon40`` login node **svol** and compute nodes::

  $ module avail CUDA/
  -------------------------- /home/modules/modules/all ---------------------------
   CUDA/11.4.1            (D)  

Additional CUDA_ software modules can be installed by user request.

Batch jobs submitted to the GPU nodes **must request GPU resources**!  
Jobs that only use CPUs without GPUs are not permitted.
Please do not use the GPU partition ``sm3090`` unless you have been authorized to use GPUs.

You **must** include batch job statements for specifying correct numbers of CPUs and GPUs.
Since the nodes in the ``sm3090`` partition have 10 GPUs each and 80 "virtual" CPU cores, 
you **must** submit jobs with 80/10 = **8 CPUs per GPU**::

  #SBATCH -n 8

For example, to submit a batch jobs to 1 GPU on 8 CPU cores of a node in the ``sm3090`` partition::

  #SBATCH --partition=sm3090
  #SBATCH -N 1-1
  #SBATCH -n 8
  #SBATCH --gres=gpu:1

For further Slurm_ information see the GRES_ page.

.. _CUDA: https://en.wikipedia.org/wiki/CUDA
.. _Tesla: https://www.nvidia.com/object/tesla-servers.html
.. _GRES: https://slurm.schedmd.com/gres.html

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

Jupyter_Notebook_ documents are documents produced by the *Jupyter Notebook App*, which contain both computer code (e.g. python) and rich text elements (paragraph, equations, figures, links, etc???). 
Notebook documents are both human-readable documents containing the analysis description and the results (figures, tables, etc..) as well as executable documents which can be run to perform data analysis.

On Niflheim we have installed Jupyter_Notebook_ software modules which you can load and use::

  $ module avail IPython
  -------------------------- /home/modules/modules/all ---------------------------
   IPython/6.4.0-foss-2018a-Python-3.6.4
   IPython/7.2.0-foss-2018b-Python-3.6.6
   IPython/7.2.0-intel-2018b-Python-3.6.6
   IPython/7.18.1-GCCcore-10.2.0          (D)

You have to select the correct *jupyter* version shown above, according to which compiler has been used to compile the other software you are using (such as GPAW).  ``7.18.1-GCCcore-10.2.0`` matches the ``foss`` and ``intel`` 2020b toolchains.

**NOTE:** If you use a virtual environment (venv), you cannot use the IPython module, as the jupyter notebook will not see the modules in the venv.  Instead you have to install jupyter in your venv (``pip install notebook``).


Restrictions on the use of Jupyter Notebook
-------------------------------------------

*  **NOTICE: Jupyter Notebooks cannot be connected to directly from any other network at DTU or outside DTU.**

* The web-server on port 8888 can only be accessed from a PC on the Fysik cabled network (including *demon*).

* The ``jupyter`` command starts a special web-server on the login node serving a network port number 8888 (plus/minus a small number).


Using Jupyter_Notebook_ documents on Niflheim from DTU Physics
--------------------------------------------------------------

1. Log in to a Niflheim login node, preferably *slid*.

2. Load the relevant module, for example::

     module load IPython/7.18.1-GCCcore-10.2.0

   venv users should **not** load this module!

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

1. Log in to a Niflheim login node, preferably *slid*.

2. Load the relevant module, for example::

     module load IPython/7.18.1-GCCcore-10.2.0

   venv users should **not** load this module!

3. Go to the relevant folder for your notebooks, and start Jupyter with the command::

      jupyter notebook --no-browser

   Jupyter will respond with around ten lines of text, at the bottom is a URL.  
   It will contain the text ``localhost:NNNN`` where NNNN is a port number, typically 8888 or close.  You need that number in the next step.

4. From your desktop/laptop, log in to niflheim again in a new window, using this command to set up an SSH tunnel::
      
      ssh -J username@jumphost -L NNNN:localhost:NNNN username@xxxx.fysik.dtu.dk -N

   where:

   * ``xxxx`` is slid or whatever machine you are using,
   * ``username`` is your DTU username,
   * ``NNNN`` is the port number printed by the notebook command,
   * ``jumphost`` is the name of the DTU Physics gateway machine.
     You need to contact Ole or your supervisor to get the actual name of the ``jumphost``, and to have your account enabled on it.

   The part ``-J username@jumphost`` can be omitted if you are using a desktop connected to the Fysik cabled network.

5. Open a browser, and cut-and-paste the address starting with ``https://localhost`` into your browser.

6. **IMPORTANT:** Once you are done using your notebooks, **remember to shut down the Jupyter server!** See point 4 
   in the instructions in the previous section (usage from DTU Physics).

.. _Jupyter_Notebook: https://jupyter-notebook-beginner-guide.readthedocs.io/en/latest/what_is_jupyter.html


Using Jupyter_Notebook_ documents on Niflheim from home/elsewhere (Windows)
---------------------------------------------------------------------------

Use these instructions when you are located outside DTU Physics, and your laptop/desktop
is running Microsoft Windows.

1. Log in to a Niflheim login node, preferably *slid*.
   Use MobaXterm to log in directly to e.g. slid.fysik.dtu.dk, but when you create the login session (the Session tab), select Network Settings, then Jump Host.  Fill in the Jump Host (and your DTU user name).

2. Load the relevant module, for example::

     module load IPython/7.18.1-GCCcore-10.2.0

   venv users should **not** load this module!

3. Go to the relevant folder for your notebooks, and start Jupyter with the command::

      jupyter notebook --no-browser --ip=$HOSTNAME

   Note the extra ``--ip`` option needed when connecting with MobaXterm.  Jupyter will respond with around ten lines of text, at the bottom is a URL.  
   It will contain the text ``localhost:NNNN`` or ``127.0.0.1:NNNN`` where NNNN is a port number, typically 8888 or close.  You need that number in the next step.

4. Use MobaXterm to set up an SSH tunnel (the Tunneling tab).
   
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
Please see the Singularity_security_ page.

A relatively new Containers_ technology created for HPC is Singularity_, developed at *Lawrence Berkeley Lab* (LBL_).
Singularity_ assumes (more or less) that each application will have its own container. 
Singularity_ assumes that you will have a build system where you are the root user, but that you will also have a production system where you may not be the root user.

To learn more about Singularity_, please see some resources:

* Singularity_ home page.
* About_Singularity_.
* Singularity_FAQ_.
* Singularity_tutorial_ page.
* Singularity_Hub_ Container Registry.
* Singularity_recipe_ for building any container.

.. _Containers: https://cloud.google.com/containers/
.. _Docker: https://www.docker.com/
.. _Singularity: https://singularity.lbl.gov/
.. _About_Singularity: https://singularity.lbl.gov/about
.. _Singularity_FAQ: https://singularity.lbl.gov/faq
.. _LBL: https:/www.lbl.gov
.. _Singularity_tutorial: https://singularity-tutorial.github.io/
.. _Singularity_Hub: https://singularity-hub.org/
.. _Singularity_recipe: https://singularity.lbl.gov/docs-recipes
.. _Singularity_security: https://singularity.lbl.gov/docs-security

Singularity on Niflheim
-----------------------

We have installed Singularity_ (current version: 3.8 from EPEL) as RPM packages.
To get started with Singularity_ it is recommended to follow the Singularity_tutorial_ page, where you may skip to *Hour 2 (Building and Running Containers)*.

You can make a test run of a Docker_ container to be executed by Singularity_::

  singularity run docker://godlovedc/lolcow

Examples of Singularity_ containers are in this directory::

  /usr/share/doc/singularity*/examples/

If you want to build and test Singularity_ containers on Niflheim, we must grant you some *sudo* priviledge - please write to the :ref:`Niflheim_support` E-mail. 

Alternatively, if you have root priviledge on your personal Linux PC, you may want to make a Singularity installation locally on the PC.
Finished containers can be copied to Niflheim, and executing Singularity_ containers is as a **normal user** without any root priviledge at all!

Please note that you must build containers within a **local file system** (not a shared file system like NFS where root access is prohibited),
so please go to a local scratch directory such as ``/scratch/$USER``).

Questions: Please write to the :ref:`Niflheim_support` E-mail. 

Running Docker containers
-------------------------

Docker_ containers can be executed under Singularity_.
For example, make a test run of a simple Docker_ container from DockerHub_::

  singularity run docker://godlovedc/lolcow

You can run many recent versions of CentOS Docker_ containers from the `CentOS library <https://hub.docker.com/r/library/centos/>`_, for example a 6.9 container::

  singularity run docker://centos:centos6.9

Ubuntu Linux may be run from the `Ubuntu library <https://hub.docker.com/_/ubuntu/>`_::

  singularity run docker://ubuntu:17.10

Application codes may also be on DockerHub_, for example an `OpenFOAM container <https://hub.docker.com/r/openfoam/>`_ can be run with::

  singularity run docker://openfoam/openfoam4-paraview50 

.. _DockerHub: https://hub.docker.com/explore/

Singularity batch jobs
----------------------

You can submit normal Slurm_ batch jobs to the queue running Singularity_ containers just like any other executable.

An example job script running a container image ``lolcow.simg``::

  #!/bin/sh
  #SBATCH --mail-type=ALL
  #SBATCH --partition=xeon16
  #SBATCH --time=05:00
  #SBATCH --output=lolcow.%J.log
  singularity exec lolcow.simg cowsay 'How did you get out of the container?'

To run a Singularity_ container in parallel on 2 nodes and 10 CPU cores with MPI use the following lines::

  #SBATCH -N 2-2
  #SBATCH -n 10
  module load OpenMPI
  mpirun -n $SLURM_NTASKS singularity exec lolcow.simg cowsay 'How did you get out of the container?'


Pages for system administrators
===============================

* `Slurm batch queueing system <https://wiki.fysik.dtu.dk/Niflheim_system/SLURM>`_.
* `Cornelis Networks Omni-Path network fabric <https://wiki.fysik.dtu.dk/Niflheim_system/OmniPath>`_.
* `EasyBuild software for environment modules on RHEL/CentOS <https://wiki.fysik.dtu.dk/Niflheim_system/EasyBuild_modules>`_.
