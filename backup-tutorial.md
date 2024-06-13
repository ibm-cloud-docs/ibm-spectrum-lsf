
## Mounting object storage to the cluster with static worker nodes
{: #hpc-cluster-mount-object-storage}
{: step}

If you have static nodes, you will need to mount your object manually for each of the nodes.

1. Access your cluster [here](https://github.ibm.com/hybrid-cloud-infrastructure-research/tracker/wiki/Access-the-cluster).

2. Get the nodes in your cluster: ``$ bhosts -w``

3. Ssh into the worker nodes: ``$ ssh icgen2hosts-IP_workernode``

4. Once you are in the login node, you can [mount your object storage](https://cloud.ibm.com/docs/cloud-object-storage?topic=cloud-object-storage-s3fs).

    a. You need ``s3fs``, which is recommended to be installed in the [extension of your base image step](https://github.ibm.com/hybrid-cloud-infrastructure-research/tracker/wiki/Extend-base-image-and-create-a-new-custom-image):
``sudo yum install -y epel-release && sudo yum install -y s3fs-fuse``

    b. Store your credentials in a file (inside your **Shared** directory) containing either ``<access_key>:<secret_key>`` or ``:<api_key>``. This file needs to have limited access so run: ``chmod 0600 <credentials_file>``

    * To create your credentials:

        1. Log in to the IBM Cloud console and navigate to your instance of Object Storage

        2. In the side navigation, click ***Service Credentials***

        3. Click ***New credential*** and provide the necessary information. If you want to generate HMAC credentials, click on ``Advanced Options`` to reveal the 'Include HMAC Credential' option. Verify the option is selected before continuing.

        4. Click ***Add*** to generate service credential

    c. Enable other users access [here](https://stackoverflow.com/questions/17544139/allowing-permission-using-s3fs-bucket-directory-for-other-users)

    * Access this file: ``sudo vim /etc/fuse.conf``
    * Uncomment this line ``user_allow_other``

    d. Mount the bucket using:
    ``s3fs <bucket> <mountpoint> -o url=http{s}://<endpoint> –o passwd_file=<credentials_file> -o allow_other -o umask=0007,uid=1000,gid=1000``
    
    If the credentials file only has an API key (no HMAC credentials), you'll need to add the ibm_iam_auth flag as well:
    ``s3fs <bucket> <mountpoint> -o url=http{s}://<endpoint> –o passwd_file=<credentials_file> -o ibm_iam_auth -o allow_other -o umask=0007,uid=1000,gid=1000``

    ***Example***: ``mkdir -p /home/lsfadmin/oklahoma-1m s3fs oklahoma-1m /home/lsfadmin/oklahoma-1m -o url=https://s3.us-east.cloud-object-storage.appdomain.cloud -o passwd_file=/home/lsfadmin/shared/credentials -o ibm_iam_auth -o allow_other -o umask=0007,uid=1000,gid=1000``

    * ``<bucket>``: name of the bucket (e.g., oklahoma-data)

    * ``<mountpoint>``: the directory where you want to mount your bucket. It has to be in the shared directories of the cluster (``/home/lsfadmin/shared/ or /mnt/data/``)

    * ``<endpoint>``: public endpoint of the bucket. To obtain it, go to Buckets > Configuration > Endpoints > public

        ***Example***: ``s3.us-south.cloud-object-storage.appdomain.cloud``

    * ``<credentials_file>``: the name of the file with the credentials

    e. List the objects in that bucket as if they were local files (or in the case of object prefixes, as if they were nested directories). Check if you have the right permissions: 
    ``drwxrwx---
ls -alrt <mountpoint>``

## Dynamic worker nodes 
{: #hpc-cluster-dynamic-worker-nodes}
{: step}

For the dynamic node, we will use the ``user_data.sh`` initialization script. This will allow the user to modify as desired the nodes which are dynamically created when auto-scaling.

1. Access the ``user_data.sh`` file: ``$ vim /opt/ibm/lsf/conf/resource_connector/ibmcloudgen2/user_data.sh``

2. Go to the end of the file and add the commands to mount your object storage:

    ``$ # Mount object storage to the dynamic nodes``

    ``$ # Allow access to other users in the node $ echo 'user_allow_other' | sudo tee -a /etc/fuse.conf``

    ``$ # Create the directory where you will mount your data (Be consistent with your static nodes if you had) $ mkdir -p /home/lsfadmin/mount_directory $ # Mount object storage to the node``

    ``$ s3fs <bucket> <mountpoint> -o url=http{s}://<endpoint> –o passwd_file=<credentials_file> -o ibm_iam_auth -o allow_other -o umask=0007,uid=1000,gid=1000``

    ***Example***:

    ``$ echo 'user_allow_other' | sudo tee -a /etc/fuse.conf``

    ``$ mkdir -p /home/lsfadmin/oklahoma-1m``
    
    ``$ s3fs oklahoma-1m /home/lsfadmin/oklahoma-1m -o url=https://s3.us-east.cloud-object-storage.appdomain.cloud -o passwd_file=/home/lsfadmin/shared/credentials -o ibm_iam_auth -o allow_other -o umask=0007,uid=1000,gid=1000``

3. To confirm that the process was correctly executed:

    a. Submit a test job: ``$ bsub -oo "/home/lsf admin/shared/out.txt" -n 1 -R "span[ptile=1]" sleep 30``

    b. Wait until the new node has been created. You can check your resource by ``$ bhosts -w`` or your job status by ``bjobs``

    c. After the node's creation, you can ssh into the node by checking the ``HOST_NAME`` on ``$ bhosts -w: $ ssh icgen2hosts-IP_workernode``

    d. Check the files in your node: ``ls -alrt``. You will find the directory that was mounted with the right permissions: ``drwxrwx---``

## COS caching to improve throughput
{: #hpc-cluster-cos-caching-throughput}
{: step}

### Caching your COS bucket
{: #hpc-cluster-cos-caching-bucket}
{: step}

When mounting a COS bucket using S3FS, it is recommended to used some advanced options to improve the performance. You can find more information [here](https://medium.com/@ozeri/file-like-access-to-ibm-cloud-object-storage-using-s3fs-f5094ed42594). 

In this section we will cover different ways of caching to improve the throughput.

S3FS offers an option to enable ``use_cache`` which will automatically maintain a local cache of files in the folder specified by ``use_cache``. Since the folder specified by ``use_cache`` is just a local cache here we explore three ways to cache in the HPC Cluster. Go [here](https://github.com/s3fs-fuse/s3fs-fuse/wiki/Fuse-Over-Amazon#details) for s3fs documentation on use_cache.

The next diagram shows three options: 

A) Caching in boot volume

B) Caching in RAM

C) Caching in the storage node

We will explain each of these options, how to execute and get a performance comparison.

### Caching in boot volume
{: #hpc-cluster-cos-caching-boot-volume}
{: step}

For the first option we will cache in the boot volume which is a volume attached to each node from the cluster with a size of 100 GB. For doing so we will set the ``use_cache=/tmp`` and the ``tmp`` directory to be located in boot.

``sudo s3fs <bucket-name> <directory-where-to-mount> -o url=https://<endpoint> -o passwd_file=<credentials_file> -o ibm_iam_auth -o allow_other -o umask=0007,uid=1000,gid=1000 -o nonempty,enable_noobj_cache,enable_content_md5,multireq_max=50 -o use_cache=/tmp``

***Example***

``sudo s3fs testing-io test-io/ -o url=https://s3.us-east.cloud-object-storage.appdomain.cloud -o passwd_file=/home/lsfadmin/shared/credentials -o ibm_iam_auth -o allow_other -o umask=0007,uid=1000,gid=1000 -o nonempty,enable_noobj_cache,enable_content_md5,multireq_max=50 -o use_cache=/tmp``

Because each worker node has its own boot volume, each worker node will have a separate and independent caching system. The size of the boot volume is 100 GB, therefore the data size being cache is limited by this, meaning that the sum of files used during execution per worker node cannot exceed 100 GB.

### Caching in RAM
{: #hpc-cluster-cos-caching-ram}
{: step}

In this option we will cache in the RAM which varies depending on the profile of the VSI. For doing so we will set the ``use_cache=/run/user/1000/`` and the ``/run/user/1000/`` directory to be located in ``tmpfs`` which is in RAM.

``sudo s3fs <bucket-name> <directory-where-to-mount> -o url=https://<endpoint> -o passwd_file=<credentials_file> -o ibm_iam_auth -o allow_other -o umask=0007,uid=1000,gid=1000 -o nonempty,enable_noobj_cache,enable_content_md5,multireq_max=50 -o use_cache=/run/user/1000/ ``

***Example***

``sudo s3fs testing-io test-io/ -o url=https://s3.us-east.cloud-object-storage.appdomain.cloud -o passwd_file=/home/lsfadmin/shared/credentials -o ibm_iam_auth -o allow_other -o umask=0007,uid=1000,gid=1000 -o nonempty,enable_noobj_cache,enable_content_md5,multireq_max=50 -o use_cache=/run/user/1000/``

In this option each worker node also have their own RAM, which means that each worker node will have a separate and independent caching system. The data size limit for this option is based on the size of the RAM, meaning that the sum of files used during execution per worker node cannot exceed the RAM size. Here you can explore the different VSI profiles and sizes of RAM for each option.

### Caching in storage mode
{: #hpc-cluster-cos-caching-storage-mode}
{: step}

In this option we will cache in the storage node which has a block volume attached and its default size is 100 GB but it can be defined by the user when building the cluster. For doing so we will set the ``use_cache=/shared/`` and the ``/``shared directory to be located in the NFS set in the HPC Cluster. You can create a cache directory inside shared by ``mkdir -p /home/lsfadmin/shared/cache``, and cache there.

``sudo s3fs <bucket-name> <directory-where-to-mount> -o url=https://<endpoint> -o passwd_file=<credentials_file> -o ibm_iam_auth -o allow_other -o umask=0007,uid=1000,gid=1000 -o nonempty,enable_noobj_cache,enable_content_md5,multireq_max=50 -o use_cache=/shared/``

***Example***

``sudo s3fs testing-io test-io/ -o url=https://s3.us-east.cloud-object-storage.appdomain.cloud -o passwd_file=/home/lsfadmin/shared/credentials -o ibm_iam_auth -o allow_other -o umask=0007,uid=1000,gid=1000 -o nonempty,enable_noobj_cache,enable_content_md5,multireq_max=50 -o use_cache=/shared``

In this option the cluster has only one storage node which means that all workers will share the cache location. The data size limit for this option is based on the size of the block volume attached to the storage node, meaning that the sum of files used during execution coming from all worker nodes cannot exceed the block volume size from the storage node.

## Performance comparison for block size
{: #hpc-cluster-performance-comparison}
{: step}

We studied the performance of these methods by using [fio](https://fio.readthedocs.io/en/latest/fio_doc.html) which is a flexible I/O tester. For the first test 100 files of 2 MB each were used with a block size of 4 KB. And we use rw, readwrite which uses sequential mixed reads and writes. The config ``fio`` file:

``[readfiles]``

``nrfiles=100``

``filesize=2MiB``

``bs=4k``

``rw=readwrite``

``directory=<directory-where-cos-mounted>``

We obtained the following results:

**No Caching**

``READ: bw=9924KiB/s (10.2MB/s), 9924KiB/s-9924KiB/s (10.2MB/s-10.2MB/s), io=95.5MiB (100MB), run=9858-9858msec``


``WRITE: bw=9889KiB/s (10.1MB/s), 9889KiB/s-9889KiB/s (10.1MB/s-10.1MB/s), io=95.2MiB (99.8MB), run=9858-9858msec``

**Caching in boot volume**

``READ: bw=40.1MiB/s (42.0MB/s), 40.1MiB/s-40.1MiB/s (42.0MB/s-42.0MB/s), io=95.5MiB (100MB), run=2385-2385msec``

``WRITE: bw=39.9MiB/s (41.9MB/s), 39.9MiB/s-39.9MiB/s (41.9MB/s-41.9MB/s), io=95.2MiB (99.8MB), run=2385-2385msec``

**Caching in RAM**

``READ: bw=36.6MiB/s (38.3MB/s), 36.6MiB/s-36.6MiB/s (38.3MB/s-38.3MB/s), io=95.5MiB (100MB), run=2613-2613msec``

``WRITE: bw=36.4MiB/s (38.2MB/s), 36.4MiB/s-36.4MiB/s (38.2MB/s-38.2MB/s), io=95.2MiB (99.8MB), run=2613-2613msec``

**Caching in storage node**


``READ: bw=35.4MiB/s (37.2MB/s), 35.4MiB/s-35.4MiB/s (37.2MB/s-37.2MB/s), io=95.5MiB (100MB), run=2696-2696msec``

``WRITE: bw=35.3MiB/s (37.0MB/s), 35.3MiB/s-35.3MiB/s (37.0MB/s-37.0MB/s), io=95.2MiB (99.8MB), run=2696-2696msec``

From this test we can see that caching improved the throughput and performance when using a COS bucket, going from 10.2MB/s to A)42.0MB/s, B)38.3MB/s, and C)37.2MB/s. As mentioned each options has a different caching orchestration and data usage limit, that the user needs to consider to select a caching method.

Comparison for multiple block sizes
{: #hpc-cluster-multiple-blocksize-comparison}
{: step}

For a general file I/O throughput test, we aim at comparing different caching options with COS by dd command. We benchmark both read and write operations with various block sizes by the following for loop commands:

``#!/bin/bash``

``block_sizes="1024 4096 16384 65536 262144 1048576"``

``# read test``

``for s in $block_sizes; do``

``dd if=<directory> of=/dev/zero bs=$s count=1000 iflag=direct``

``done``

``# write test``

``for s in $block_sizes; do``

``dd if=/dev/zero of=<directory> bs=$s count=1000 oflag=direct``

``done``

Before every reading test, we drop the system cache first by the following command, and the reported results later in this section are after the first warm-up run.

``sudo /sbin/sysctl -w vm.drop_caches=3``

The read and write bandwidths are presented in Table 1 A&B. For file reading, using local folders, especially in host boot volume and RAM, for caching is much faster than no caching, since we already download the files and can directly read the cache files. 

For file writing, the best practices we can get is caching in RAM disk and the improvement is most significant for large block sizes. Note that caching to NFS, which is a low efficiency file system, even slows down the writing bandwidth. The writing throughput is not as fast as reading, which we assume the potential bottleneck is in the synchronization phase to the COS bucket.

## Mounting object storage to the cluster with static worker nodes
{: #hpc-cluster-mount-object-storage}
{: step}

If you have static nodes, you will need to mount your object manually for each of the nodes.

1. Access your cluster [here](https://github.ibm.com/hybrid-cloud-infrastructure-research/tracker/wiki/Access-the-cluster).

2. Get the nodes in your cluster: ``$ bhosts -w``

3. Ssh into the worker nodes: ``$ ssh icgen2hosts-IP_workernode``

4. Once you are in the login node, you can [mount your object storage](https://cloud.ibm.com/docs/cloud-object-storage?topic=cloud-object-storage-s3fs).

    a. You need ``s3fs``, which is recommended to be installed in the [extension of your base image step](https://github.ibm.com/hybrid-cloud-infrastructure-research/tracker/wiki/Extend-base-image-and-create-a-new-custom-image):
``sudo yum install -y epel-release && sudo yum install -y s3fs-fuse``

    b. Store your credentials in a file (inside your **Shared** directory) containing either ``<access_key>:<secret_key>`` or ``:<api_key>``. This file needs to have limited access so run: ``chmod 0600 <credentials_file>``

    * To create your credentials:

        1. Log in to the IBM Cloud console and navigate to your instance of Object Storage

        2. In the side navigation, click ***Service Credentials***

        3. Click ***New credential*** and provide the necessary information. If you want to generate HMAC credentials, click on ``Advanced Options`` to reveal the 'Include HMAC Credential' option. Verify the option is selected before continuing.

        4. Click ***Add*** to generate service credential

    c. Enable other users access [here](https://stackoverflow.com/questions/17544139/allowing-permission-using-s3fs-bucket-directory-for-other-users)

    * Access this file: ``sudo vim /etc/fuse.conf``
    * Uncomment this line ``user_allow_other``

    d. Mount the bucket using:
    ``s3fs <bucket> <mountpoint> -o url=http{s}://<endpoint> –o passwd_file=<credentials_file> -o allow_other -o umask=0007,uid=1000,gid=1000``
    
    If the credentials file only has an API key (no HMAC credentials), you'll need to add the ibm_iam_auth flag as well:
    ``s3fs <bucket> <mountpoint> -o url=http{s}://<endpoint> –o passwd_file=<credentials_file> -o ibm_iam_auth -o allow_other -o umask=0007,uid=1000,gid=1000``

    ***Example***: ``mkdir -p /home/lsfadmin/oklahoma-1m s3fs oklahoma-1m /home/lsfadmin/oklahoma-1m -o url=https://s3.us-east.cloud-object-storage.appdomain.cloud -o passwd_file=/home/lsfadmin/shared/credentials -o ibm_iam_auth -o allow_other -o umask=0007,uid=1000,gid=1000``

    * ``<bucket>``: name of the bucket (e.g., oklahoma-data)

    * ``<mountpoint>``: the directory where you want to mount your bucket. It has to be in the shared directories of the cluster (``/home/lsfadmin/shared/ or /mnt/data/``)

    * ``<endpoint>``: public endpoint of the bucket. To obtain it, go to Buckets > Configuration > Endpoints > public

        ***Example***: ``s3.us-south.cloud-object-storage.appdomain.cloud``

    * ``<credentials_file>``: the name of the file with the credentials

    e. List the objects in that bucket as if they were local files (or in the case of object prefixes, as if they were nested directories). Check if you have the right permissions: 
    ``drwxrwx---
ls -alrt <mountpoint>``

## Dynamic worker nodes 
{: #hpc-cluster-dynamic-worker-nodes}
{: step}

For the dynamic node, we will use the ``user_data.sh`` initialization script. This will allow the user to modify as desired the nodes which are dynamically created when auto-scaling.

1. Access the ``user_data.sh`` file: ``$ vim /opt/ibm/lsf/conf/resource_connector/ibmcloudgen2/user_data.sh``

2. Go to the end of the file and add the commands to mount your object storage:

    ``$ # Mount object storage to the dynamic nodes``

    ``$ # Allow access to other users in the node $ echo 'user_allow_other' | sudo tee -a /etc/fuse.conf``

    ``$ # Create the directory where you will mount your data (Be consistent with your static nodes if you had) $ mkdir -p /home/lsfadmin/mount_directory $ # Mount object storage to the node``

    ``$ s3fs <bucket> <mountpoint> -o url=http{s}://<endpoint> –o passwd_file=<credentials_file> -o ibm_iam_auth -o allow_other -o umask=0007,uid=1000,gid=1000``

    ***Example***:

    ``$ echo 'user_allow_other' | sudo tee -a /etc/fuse.conf``

    ``$ mkdir -p /home/lsfadmin/oklahoma-1m``
    
    ``$ s3fs oklahoma-1m /home/lsfadmin/oklahoma-1m -o url=https://s3.us-east.cloud-object-storage.appdomain.cloud -o passwd_file=/home/lsfadmin/shared/credentials -o ibm_iam_auth -o allow_other -o umask=0007,uid=1000,gid=1000``

3. To confirm that the process was correctly executed:

    a. Submit a test job: ``$ bsub -oo "/home/lsf admin/shared/out.txt" -n 1 -R "span[ptile=1]" sleep 30``

    b. Wait until the new node has been created. You can check your resource by ``$ bhosts -w`` or your job status by ``bjobs``

    c. After the node's creation, you can ssh into the node by checking the ``HOST_NAME`` on ``$ bhosts -w: $ ssh icgen2hosts-IP_workernode``

    d. Check the files in your node: ``ls -alrt``. You will find the directory that was mounted with the right permissions: ``drwxrwx---``

## COS caching to improve throughput
{: #hpc-cluster-cos-caching-throughput}
{: step}

### Caching your COS bucket
{: #hpc-cluster-cos-caching-bucket}
{: step}

When mounting a COS bucket using S3FS, it is recommended to used some advanced options to improve the performance. You can find more information [here](https://medium.com/@ozeri/file-like-access-to-ibm-cloud-object-storage-using-s3fs-f5094ed42594). 

In this section we will cover different ways of caching to improve the throughput.

S3FS offers an option to enable ``use_cache`` which will automatically maintain a local cache of files in the folder specified by ``use_cache``. Since the folder specified by ``use_cache`` is just a local cache here we explore three ways to cache in the HPC Cluster. Go [here](https://github.com/s3fs-fuse/s3fs-fuse/wiki/Fuse-Over-Amazon#details) for s3fs documentation on use_cache.

The next diagram shows three options: 

A) Caching in boot volume

B) Caching in RAM

C) Caching in the storage node

We will explain each of these options, how to execute and get a performance comparison.

### Caching in boot volume
{: #hpc-cluster-cos-caching-boot-volume}
{: step}

For the first option we will cache in the boot volume which is a volume attached to each node from the cluster with a size of 100 GB. For doing so we will set the ``use_cache=/tmp`` and the ``tmp`` directory to be located in boot.

``sudo s3fs <bucket-name> <directory-where-to-mount> -o url=https://<endpoint> -o passwd_file=<credentials_file> -o ibm_iam_auth -o allow_other -o umask=0007,uid=1000,gid=1000 -o nonempty,enable_noobj_cache,enable_content_md5,multireq_max=50 -o use_cache=/tmp``

***Example***

``sudo s3fs testing-io test-io/ -o url=https://s3.us-east.cloud-object-storage.appdomain.cloud -o passwd_file=/home/lsfadmin/shared/credentials -o ibm_iam_auth -o allow_other -o umask=0007,uid=1000,gid=1000 -o nonempty,enable_noobj_cache,enable_content_md5,multireq_max=50 -o use_cache=/tmp``

Because each worker node has its own boot volume, each worker node will have a separate and independent caching system. The size of the boot volume is 100 GB, therefore the data size being cache is limited by this, meaning that the sum of files used during execution per worker node cannot exceed 100 GB.

### Caching in RAM
{: #hpc-cluster-cos-caching-ram}
{: step}

In this option we will cache in the RAM which varies depending on the profile of the VSI. For doing so we will set the ``use_cache=/run/user/1000/`` and the ``/run/user/1000/`` directory to be located in ``tmpfs`` which is in RAM.

``sudo s3fs <bucket-name> <directory-where-to-mount> -o url=https://<endpoint> -o passwd_file=<credentials_file> -o ibm_iam_auth -o allow_other -o umask=0007,uid=1000,gid=1000 -o nonempty,enable_noobj_cache,enable_content_md5,multireq_max=50 -o use_cache=/run/user/1000/ ``

***Example***

``sudo s3fs testing-io test-io/ -o url=https://s3.us-east.cloud-object-storage.appdomain.cloud -o passwd_file=/home/lsfadmin/shared/credentials -o ibm_iam_auth -o allow_other -o umask=0007,uid=1000,gid=1000 -o nonempty,enable_noobj_cache,enable_content_md5,multireq_max=50 -o use_cache=/run/user/1000/``

In this option each worker node also have their own RAM, which means that each worker node will have a separate and independent caching system. The data size limit for this option is based on the size of the RAM, meaning that the sum of files used during execution per worker node cannot exceed the RAM size. Here you can explore the different VSI profiles and sizes of RAM for each option.

### Caching in storage mode
{: #hpc-cluster-cos-caching-storage-mode}
{: step}

In this option we will cache in the storage node which has a block volume attached and its default size is 100 GB but it can be defined by the user when building the cluster. For doing so we will set the ``use_cache=/shared/`` and the ``/``shared directory to be located in the NFS set in the HPC Cluster. You can create a cache directory inside shared by ``mkdir -p /home/lsfadmin/shared/cache``, and cache there.

``sudo s3fs <bucket-name> <directory-where-to-mount> -o url=https://<endpoint> -o passwd_file=<credentials_file> -o ibm_iam_auth -o allow_other -o umask=0007,uid=1000,gid=1000 -o nonempty,enable_noobj_cache,enable_content_md5,multireq_max=50 -o use_cache=/shared/``

***Example***

``sudo s3fs testing-io test-io/ -o url=https://s3.us-east.cloud-object-storage.appdomain.cloud -o passwd_file=/home/lsfadmin/shared/credentials -o ibm_iam_auth -o allow_other -o umask=0007,uid=1000,gid=1000 -o nonempty,enable_noobj_cache,enable_content_md5,multireq_max=50 -o use_cache=/shared``

In this option the cluster has only one storage node which means that all workers will share the cache location. The data size limit for this option is based on the size of the block volume attached to the storage node, meaning that the sum of files used during execution coming from all worker nodes cannot exceed the block volume size from the storage node.

## Performance comparison for block size
{: #hpc-cluster-performance-comparison}
{: step}

We studied the performance of these methods by using [fio](https://fio.readthedocs.io/en/latest/fio_doc.html) which is a flexible I/O tester. For the first test 100 files of 2 MB each were used with a block size of 4 KB. And we use rw, readwrite which uses sequential mixed reads and writes. The config ``fio`` file:

``[readfiles]``

``nrfiles=100``

``filesize=2MiB``

``bs=4k``

``rw=readwrite``

``directory=<directory-where-cos-mounted>``

We obtained the following results:

**No Caching**

``READ: bw=9924KiB/s (10.2MB/s), 9924KiB/s-9924KiB/s (10.2MB/s-10.2MB/s), io=95.5MiB (100MB), run=9858-9858msec``


``WRITE: bw=9889KiB/s (10.1MB/s), 9889KiB/s-9889KiB/s (10.1MB/s-10.1MB/s), io=95.2MiB (99.8MB), run=9858-9858msec``

**Caching in boot volume**

``READ: bw=40.1MiB/s (42.0MB/s), 40.1MiB/s-40.1MiB/s (42.0MB/s-42.0MB/s), io=95.5MiB (100MB), run=2385-2385msec``

``WRITE: bw=39.9MiB/s (41.9MB/s), 39.9MiB/s-39.9MiB/s (41.9MB/s-41.9MB/s), io=95.2MiB (99.8MB), run=2385-2385msec``

**Caching in RAM**

``READ: bw=36.6MiB/s (38.3MB/s), 36.6MiB/s-36.6MiB/s (38.3MB/s-38.3MB/s), io=95.5MiB (100MB), run=2613-2613msec``

``WRITE: bw=36.4MiB/s (38.2MB/s), 36.4MiB/s-36.4MiB/s (38.2MB/s-38.2MB/s), io=95.2MiB (99.8MB), run=2613-2613msec``

**Caching in storage node**


``READ: bw=35.4MiB/s (37.2MB/s), 35.4MiB/s-35.4MiB/s (37.2MB/s-37.2MB/s), io=95.5MiB (100MB), run=2696-2696msec``

``WRITE: bw=35.3MiB/s (37.0MB/s), 35.3MiB/s-35.3MiB/s (37.0MB/s-37.0MB/s), io=95.2MiB (99.8MB), run=2696-2696msec``

From this test we can see that caching improved the throughput and performance when using a COS bucket, going from 10.2MB/s to A)42.0MB/s, B)38.3MB/s, and C)37.2MB/s. As mentioned each options has a different caching orchestration and data usage limit, that the user needs to consider to select a caching method.

Comparison for multiple block sizes
{: #hpc-cluster-multiple-blocksize-comparison}
{: step}

For a general file I/O throughput test, we aim at comparing different caching options with COS by dd command. We benchmark both read and write operations with various block sizes by the following for loop commands:

``#!/bin/bash``

``block_sizes="1024 4096 16384 65536 262144 1048576"``

``# read test``

``for s in $block_sizes; do``

``dd if=<directory> of=/dev/zero bs=$s count=1000 iflag=direct``

``done``

``# write test``

``for s in $block_sizes; do``

``dd if=/dev/zero of=<directory> bs=$s count=1000 oflag=direct``

``done``

Before every reading test, we drop the system cache first by the following command, and the reported results later in this section are after the first warm-up run.

``sudo /sbin/sysctl -w vm.drop_caches=3``

The read and write bandwidths are presented in Table 1 A&B. For file reading, using local folders, especially in host boot volume and RAM, for caching is much faster than no caching, since we already download the files and can directly read the cache files. 

For file writing, the best practices we can get is caching in RAM disk and the improvement is most significant for large block sizes. Note that caching to NFS, which is a low efficiency file system, even slows down the writing bandwidth. The writing throughput is not as fast as reading, which we assume the potential bottleneck is in the synchronization phase to the COS bucket.
