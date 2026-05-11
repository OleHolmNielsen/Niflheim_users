=================================
 Home storage vs Scratch storage
=================================

Niflheim provides two main types of user storage: **home storage** and **scratch storage**. These file systems are configured the same way in terms of access and day-to-day use. The key difference is **backup**.

Home storage (backed up)
========================

Use your home area (for example ``/home/niflheim`` or ``/home/energy``) for data that should be preserved and can be difficult or time-consuming to recreate.

Typical examples include:

* Source code and scripts
* Job submission files
* Configuration files
* Notes, documentation, and small analysis results
* Curated datasets you intend to keep
* Final figures and processed output

Because home storage is backed up, it is the right place for important files and long-term project data.

Scratch storage (not backed up)
===============================

Use scratch areas for temporary or high-volume data generated during active work or for multi-node applications which require the use of scratch files. We provide scratch file spaces at::

  /home/scratch3/$USER/         # CAMD, CatTheory, Energy groups
  /home/scratch11/$USER/        # Construct/MEK group
  /home/scratch12/$USER/        # Energy group

Typical use of scratch include:

* Running calculations and simulations
* Intermediate files
* Large temporary datasets
* Output that still needs analysis

**REMEMBER:** There is **no backup** of files!!
Lost files cannot be recovered by any means!

Please remember to clean up scratch files regularly when they are no longer needed.


.. _compute_node_tmp:

Local disk on compute nodes (``/tmp``)
======================================

Each compute node also provides local temporary disk space mounted at ``/tmp`` and users are kindly requested to configure job scripts to use the compute nodes' ``/tmp`` folder for any temporary files in the job.

This storage is intended for temporary files used during a running job, for example:

* Fast temporary read/write data
* Application scratch files
* Temporary intermediate files needed only while the job runs

Files in ``/tmp`` are local to the compute node, are not shared across nodes, and should be considered temporary. Important data should be copied elsewhere before the job finishes. The use of ``/tmp`` is thus only applicable for multinode jobs if the software is able to handle node-local scratch folders.

Much software implements use of the local disk by setting an environment variable via a job script command::

  export TMPDIR=/tmp

This ``$TMPDIR`` setting is the default value in many computer codes and may not need to be set explicitly, check the software documentation. See :ref:`tmp_example` for examples on how to use ``/tmp`` actively.

Notes:

* On the **login nodes** you **must not** use ``/tmp`` for large files!
  Please use instead the local ``/scratch/$USER`` folder.

Technical details:

* Each Slurm job automatically allocates a **temporary** ``/tmp`` disk space which is private to the job in question.
* This temporary disk space lives only for the duration of the Slurm job, and is automatically deleted when the job terminates.
* This temporary disk space is actually allocated on the compute node's local ``/scratch`` disk, the size of which is specified under the :ref:`compute_node_partitions` section.
* The **local node scratch disk space** is **shared** between all Slurm jobs currently running on the node.
* The ``xeon32_4096`` nodes are equipped with a very large (14 TB) and very fast scratch file system. Large scratch spaces are typically required by big-memory jobs.

Recommended workflow
====================

#. Run jobs and store temporary output in scratch storage.
#. Use ``/tmp`` for node-local temporary files during execution when appropriate.
#. Review and analyze the results.
#. Keep only the valuable or final data.
#. Move curated results to your home or project storage for long-term retention.

Examples
========

Use a folder under ``/home/scratchX`` for calculation output
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Use a mirrored folder under ``/home/scratchX/username`` for calculation
outputs with the same relative path as under ``/home/energy/username``,
e.g. ``/home/energy/username/calc/folder`` turns into
``/home/scratchX/username/calc/folder``.

.. code-block:: bash
               
   #!/bin/bash
   
   # Slurm instructions
   #SBATCH ...
   
   # The scratch base folder
   SCRATCHDIR='/home/scratch12/<USER>'
   
   current="$PWD"
   home="$HOME"
   
   # Remove $HOME prefix
   relative="${current#$home/}"
   
   export CALCDIR="$SCRATCHDIR/$relative"
   mkdir -p $CALCDIR
   
   # Copy input files to calculation folder
   cp ${SLURM_SUBMIT_DIR}/{INCAR,POSCAR,POTCAR,KPOINTS} $CALCDIR/
   cd $CALCDIR
   
   # Load modules
   module purge
   module load VASP/6.6.0-intel-2025b
   
   # Run the calculation
   srun vasp_std > vasp.out
   
.. _tmp_example:
   
Using /tmp as calculation directory
+++++++++++++++++++++++++++++++++++

The compute nodes' local disk is by far the fastest option for
temporary or output files.

.. code-block:: bash

   #!/bin/bash
    
   # Slurm instructions
   #SBATCH ...
   
   # Set calc dir
   export TMPDIR='/tmp'
   
   cp ${SLURM_SUBMIT_DIR}/{INCAR,POSCAR,POTCAR,KPOINTS} $TMPDIR/
   cd $TMPDIR
   
   module ...
   
   srun vasp_std > vasp.out
   
Using ASE
+++++++++

With ASE the submit script differs slightly.

.. code-block:: bash
                
   #!/bin/bash
    
   # Slurm instructions
   #SBATCH ...
   
   module ...
   module load ASE/3.28.0-iimkl-2025b
   
   python3 <script.py>

In the python script you need to tell the calculator where to put
temporary files or save calculation results, the exact method may
differ for other calculators.

.. code-block:: python
                
   from pathlib import Path

   # Using a folder on a scratch server
   SCRATCH_FOLDER = Path('/home/scratchX/<USER>/')
   relative = Path.cwd().relative_to(Path.home())
   calc_dir = SCRATCH_FOLDER / relative

   # Using /tmp
   calc_dir = '/tmp'
   
   # Set output dir in VASP calculators
   calc = Vasp(atoms=...,
             directory=calc_dir,
             ...)

   # Saving output with GPAW
   calc.attach(calc.write, 5, calc_dir / 'xyz.gpw')

Copying results from /tmp to home folder
++++++++++++++++++++++++++++++++++++++++

Since contents of /tmp is deleted after the job ends, relevant output
should be copied to a user folder when the job ends.

.. code-block:: bash

   #!/bin/bash
   
   # Slurm instructions
   #SBATCH ...

   # Set calc dir
   export TMPDIR='/tmp'
   
   # Copy input files to calculation folder
   cp ${SLURM_SUBMIT_DIR}/{INCAR,POSCAR,POTCAR,KPOINTS} $TMPDIR/
   cd $TMPDIR
   
   module ...
   
   function term_handler() {
       for f in OUTCAR vasp.out <other-important-files>; do
           cp $TMPDIR/$f $SLURM_SUBMIT_DIR/;
       done
   }

   trap term_handler TERM
   
   srun vasp_std > vasp.out
   # or for a python script
   # python3 <script.py>
   
Copying files back at specific time
+++++++++++++++++++++++++++++++++++

Note that Slurm only gives the TERM handler 30 seconds to finish its
tasks, e.g. copy back files. If this is too little for the file
transfer to finish you can signal to copy back at a specific time.
   
.. code-block:: bash

   #!/bin/bash
   
   # Slurm instructions
   #SBATCH ...
   #SBATCH --signal=B:USR1@125  # Signalling USR1 125 seconds before the walltime ends

   function term_handler() ...
   
   trap term_handler USR1
   
   # We need to do the calculation in a background process and wait
   # while it runs
   srun vasp_std > vasp.out &
   # or for a python script
   # python3 <script.py> &
   PID=$!
   
   while kill -0 "$PID" 2>/dev/null; do
       wait "$PID"
   done
   
If we only had wait and nothing else the bash wait command could get
interrupted by the signal and then move to the next line. The while
loop actively checks if the process is still running and waits for
it. From the bash manual
(https://www.gnu.org/software/bash/manual/html_node/Signals.html):

 If Bash is waiting for a command to complete and receives a signal for
 which a trap has been set, it will not execute the trap until the
 command completes. If Bash is waiting for an asynchronous command via
 the wait builtin, and it receives a signal for which a trap has been
 set, the wait builtin will return immediately with an exit status
 greater than 128, immediately after which the shell executes the trap
