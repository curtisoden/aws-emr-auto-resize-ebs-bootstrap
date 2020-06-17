# aws-emr-auto-resize-ebs-bootstrap
EMR bootstrap Script to auto-resize EMR volumes

##Author
Curtis Oden - Email: coden@ata-llc.com | Web: ata-llc.com

## Repo home
https://github.com/curtisoden/aws-emr-auto-resize-ebs-bootstrap

##Background

This repo was originally forked from Forked from https://github.com/andr-t/aws-emr-resize_storage_nvme, which is based off of the code referenced in the AWS Big Data Blog article "Dynamically scale up storage on Amazon EMR clusters" (https://aws.amazon.com/blogs/big-data/dynamically-scale-up-storage-on-amazon-emr-clusters/). Unfortunately, the S3 reference in the article (s3://aws-bigdata-blog/artifacts/resize_storage/resize_storage.sh) does not seem to work.

This repo includes andr-t's enhancement to support nvme volumes, which are used in AWS latest instance classes.

To use this script, upload resize_storage.sh to an S3 bucket to which your EMR cluster has appropriate IAM S3 policies to access. 

Here is an excerpt from the article that provides more detail on the problem context and usage:

###Dynamically resize the storage space on core and task nodes
To scale up the storage of core and task nodes on your cluster, use the following bootstrap action script:

s3://aws-bigdata-blog/artifacts/resize_storage/resize_storage.sh

Additionally, the EC2 instance profile of your cluster must have the ec2:ModifyVolume permissions to be able to resize the volume.

The script runs on all the nodes of an EMR cluster. It configures a cron job on the nodes and performs a disk utilization check every 2 minutes. On the master node, it performs the check on the root volume and the volumes that are storing the logs of various master daemons. On core and task nodes, it performs the check on volumes that YARN and HDFS use and determines whether there is a need to scale up the storage.

When it determines that a volume has exceeded 90 percent of its usage, the size of the volume is expanded by the percentage specified by the “--scaling-factor” parameter. During the resize process, the partition of the volume is expanded, and the file system is extended to reflect the updated capacity. All of this happens without affecting the applications that are running on the cluster.

Consider the following caveats before using this solution:

You can scale up the storage capacity of nodes in an EMR cluster only if the cluster uses EBS volumes as its storage backend. Certain EC2 instance types use only instance store volumes or both instance store and EBS volumes. You can’t resize the storage capacity on clusters that use such EC2 instance types.
While you are deciding on the scaling factor option of the script, plan ahead to increase the volume so that the updated configuration will last for quite some time. The scaling up of storage has to wait at least 6 hours before applying further modifications to the same volume.

