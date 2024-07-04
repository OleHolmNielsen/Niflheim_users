.. _Hardware:

====================
Hardware information
====================

.. Contents::

Niflheim overview
=================

Niflheim currently (as of February 2024) consists of the following hardware:

* A total of **700 compute nodes**.
* The nodes contain a total of **31000 CPU cores**.
* Aggregate theoretical peak performance of more than **22XX TeraFLOPS** (TFLOPS_) or **2.2 PetaFLOPS**.
* Total RAM memory is 300+ TB.

.. _TFLOPS: http://en.wikipedia.org/wiki/FLOPS

CPU architectures
=================

Niflheim comprises several generations of hardware for a total of **700 compute nodes**:

Lenovo AMD EPYC 9474F servers
------------------------------

* **68 96-core nodes** Lenovo SD665_V3_ with two AMD EPYC Zen4_ 9474F_ 48-core CPUs (a total of 6528 cores) running at 3.60 GHz Base frequency (up to 4.10 GHz in Boost Clock) and including AVX-512_ vector instructions.
  The Direct Water Cooled servers are mounted in *Neptune* DW612S_ chasisses.

  Peak speed: 6500 GFLOPS/node, 442 TFLOPS_ in total for all nodes.

  The RAM memory is 768 GB (8.0 GB/core) 4800 MHz DDR5 dual-rank memory, 52.2 TB in total for all nodes.

  Each server has a 1.9 TB local NVMe hard disk.

  The network interconnect is **100 Gbit/s** water-cooled
  `ThinkSystem NVIDIA ConnectX-7 NDR200 InfiniBand QSFP112 Adapters <https://lenovopress.lenovo.com/lp1693-thinksystem-nvidia-connectx-7-ndr200-infiniband-qsfp112-adapters>`_.

  Installed in **February 2024**.

.. _SD665_V3: https://lenovopress.lenovo.com/lp1612-lenovo-thinksystem-sd665-v3-server
.. _DW612S: https://pubs.lenovo.com/dw612s_neptune_enclosure/
.. _9474F: https://www.amd.com/en/products/processors/server/epyc/4th-generation-9004-and-8004-series/amd-epyc-9474f.html
.. _Zen4: https://www.amd.com/en/campaigns/epyc-9004-architecture

Lenovo Intel servers with 4 TB RAM memory
--------------------------------------------

* **4 32-core nodes** Lenovo SR850_V3_ servers with 4 TB RAM memory per node and a 15 TB local NVMe scratch disk.
  The four processors are Intel(R) Xeon(R) Gold 6434H.

  Installed in **February 2024**.

.. _SR850_V3: https://lenovopress.lenovo.com/lp1605-thinksystem-sr850-v3-server

Lenovo GPU servers with 4xA100 SXM4
---------------------------------------

* **4 GPU nodes** Lenovo SD650-N_V2_ 1U servers that feature the Intel Xeon family of processors and 4 times NVIDIA A100_ *A100-SXM4-40GB* GPUs and an NVLink_ interconnect. 
  The processors are Intel Xeon Platinum 8358 CPUs with 512 GB of RAM memory per node.
  The Direct Water Cooled servers are mounted in *Neptune* DW612S_ chasisses.

  Installed in **February 2024**.

.. _SD650-N_V2: https://lenovopress.lenovo.com/lp1613-thinksystem-sd650-n-v2-server
.. _A100: https://www.nvidia.com/en-us/data-center/a100/
.. _NVLink: https://en.wikipedia.org/wiki/NVLink

Dell Ice Lake servers
---------------------

* **96 56-core nodes** Dell R650_ with two Intel IceLake_ Xeon_Gold_6348_ 28-core CPUs (a total of 5376 cores) running at 2.60 GHz Base frequency (up to 3.50 GHz in Turbo_mode_) and including AVX-512_ vector instructions.

  Peak speed: 4659 GFLOPS/node, 447 TFLOPS_ in total for all nodes.

  The RAM memory is 512 GB (9.14 GB/core) 3200 MHz DDR4 dual-rank memory, 49.1 TB in total for all nodes.

  Each server has a 480 GB local SSD hard disk.

  The network interconnect is **100 Gbit/s** Cornelis_Networks_ (previously Intel) OmniPath_.

  Installed in **December 2021**.

SuperMicro servers
------------------

* **8 40-core nodes** SYS-4029GP-TRT2_ from Nextron/SuperMicro with Intel Xeon_Gold_5218R_ 20-core CPUs @2.10 GHz (a total of 320 cores).
  Hyperthreading is enabled so that each node has 80 threads or virtual CPUs.

  Peak speed: 2688 GFLOPS/node, 21 TFLOPS_ in total for all nodes.

  The RAM memory is 198 GB (4 nodes) and 768 GB (4 nodes), 3.81 TB in total for all nodes.

  These servers are equipped with Nvidia GPUs.

  Each server has a 960 GB local NVMe hard disk and a 1 Gbit Ethernet.

  Installed in **December 2020 and July 2021**.

.. _SYS-4029GP-TRT2: https://www.supermicro.com/en/products/system/4U/4029/SYS-4029GP-TRT2.cfm

Dell Cascade Lake Refresh servers
---------------------------------

* **128 40-core nodes** Dell R640_ with two Intel Cascade_Lake_ Xeon_Gold_6242R_ 20-core CPUs (a total of 5120 cores) running at 3.10 GHz Base frequency (up to 4.10 GHz in Turbo_mode_) and including AVX-512_ vector instructions.

  Peak speed: 3968 GFLOPS/node, 508 TFLOPS_ in total for all nodes.

  The RAM memory is 384 GB (9.6 GB/core) 2933 MHz DDR4 dual-rank memory, 49.1 TB in total for all nodes.

  Each server has a 240 GB local SSD hard disk.

  The network interconnect is **100 Gbit/s** Cornelis_Networks_ (previously Intel) OmniPath_.

  Installed in **October 2020**.

Dell Skylake servers
--------------------

* **208 40-core nodes** Dell C6420_ and R640_ with two Intel Skylake_ Xeon_Gold_6148_ 20-core CPUs (a total of 8320 cores) running at 2.40 GHz Base frequency (up to 3.70 GHz in Turbo_mode_) and including AVX-512_ vector instructions.

  Peak speed: 3072 GFLOPS/node, 639 TFLOPS_ in total for all nodes.

  The RAM memory type is 2666 MHz DDR4 dual-rank memory:

  * 196 C6420_ nodes have 384 GB of memory (9.6 GB/core), 75.3 TB in total for all nodes.
  * 12 R640_ nodes have 768 GB of memory (19.2 GB/core), 9.2 TB in total for all nodes.

  Each server has a 240 GB local SSD hard disk.

  The network interconnect is **100 Gbit/s** Cornelis_Networks_ (previously Intel) OmniPath_.

  Installed in **April 2019**.

Huawei Broadwell servers
------------------------

* **192 24-core nodes** `Huawei XH620 v3 <http://e.huawei.com/en/products/cloud-computing-dc/servers/x-series/xh620-v3>`_
  with two Intel Broadwell_ Xeon_E5-2650_v4_ 12-core CPUs (a total of 4608 cores) running at 2.20 GHz (up to 2.90 GHz in Turbo_mode_).

  Peak speed: 845 GFLOPS/node, 162 TFLOPS_ in total for all nodes.

  The RAM memory type is 2400 MHz DDR4 dual-rank memory:

  * 180 nodes have 256 GB of memory (10.7 GB/core), 46.1 TB in total for all nodes.
  * 12 nodes have 512 GB of memory (21.3 GB/core), 6.1 TB in total for all nodes.

  Each server has a 240 GB local SSD hard disk.

  The network interconnect is **100 Gbit/s** Cornelis_Networks_ (previously Intel) OmniPath_.

  Installed in **December 2016, March 2017, November 2017**.

.. _OmniPath: https://www.cornelisnetworks.com/products/
.. _Cornelis_Networks: https://www.cornelisnetworks.com/
.. _Infiniband: http://en.wikipedia.org/wiki/InfiniBand
.. _IceLake: https://en.wikipedia.org/wiki/Ice_Lake_(microprocessor)
.. _Cascade_Lake: https://en.wikipedia.org/wiki/Cascade_Lake_(microarchitecture)
.. _Skylake: https://en.wikipedia.org/wiki/Skylake_(microarchitecture)
.. _Broadwell: https://en.wikipedia.org/wiki/Broadwell_%28microarchitecture%29
.. _GPU: http://en.wikipedia.org/wiki/Graphics_processing_unit
.. _AVX-512: https://en.wikipedia.org/wiki/AVX-512
.. _Xeon_Gold_6348: https://www.intel.com/content/www/us/en/products/sku/212456/intel-xeon-gold-6348-processor-42m-cache-2-60-ghz/specifications.html
.. _Xeon_Gold_5218R: https://ark.intel.com/content/www/us/en/ark/products/199342/intel-xeon-gold-5218r-processor-27-5m-cache-2-10-ghz.html
.. _Xeon_Gold_6242R: https://ark.intel.com/content/www/us/en/ark/products/199352/intel-xeon-gold-6242r-processor-35-75m-cache-3-10-ghz.html
.. _Xeon_Gold_6148: https://ark.intel.com/content/www/us/en/ark/products/120489/intel-xeon-gold-6148-processor-27-5m-cache-2-40-ghz.html
.. _Xeon_E5-2650_v4: https://ark.intel.com/content/www/us/en/ark/products/91767/intel-xeon-processor-e5-2650-v4-30m-cache-2-20-ghz.html
.. _Xeon_E5-2650_v2: https://ark.intel.com/content/www/us/en/ark/products/75269/intel-xeon-processor-e5-2650-v2-20m-cache-2-60-ghz.html
.. _Xeon_E5-2670: https://ark.intel.com/content/www/us/en/ark/products/64595/intel-xeon-processor-e5-2670-20m-cache-2-60-ghz-8-00-gt-s-intel-qpi.html
.. _Xeon_X5550: https://ark.intel.com/content/www/us/en/ark/products/37106/intel-xeon-processor-x5550-8m-cache-2-66-ghz-6-40-gt-s-intel-qpi.html
.. _Xeon_X5570: https://ark.intel.com/content/www/us/en/ark/products/37111/intel-xeon-processor-x5570-8m-cache-2-93-ghz-6-40-gt-s-intel-qpi.html
.. _C6420: https://www.dell.com/en-us/work/shop/povw/poweredge-c6420
.. _R640: https://www.dell.com/en-us/work/shop/povw/poweredge-r640
.. _R650: https://www.dell.com/en-us/work/shop/povw/poweredge-r650
.. _Turbo_mode: https://en.wikipedia.org/wiki/Intel_Turbo_Boost

File servers
============

Several Linux file servers are available for the departmental user groups.
Each group is assigned a file-system on one of the existing file servers.
Depending on disk requirements, group file-systems can be from 1 TB and up.

The file servers are standard Linux servers with large disk arrays, sharing the file-systems using NFS.
We do not use any parallel file servers (for example, Lustre_ etc.). 

The file server total available disk spaces are:

* Server niflfs1: 108 TB
* Server niflfs3: 87 TB
* Server niflfs4: 90 TB
* Server niflfs5: 90 TB
* Server niflfs6: 106 TB
* Server niflfs7: 106 TB
* Server niflfs8: 163 TB
* Server niflfs9: 163 TB

A maximum disk capacity of 913 TB disk space is available for user applications.

.. _Lustre: https://en.wikipedia.org/wiki/Lustre_%28file_system%29
