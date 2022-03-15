# Immutability
TrilioVault for Kubernetes provides the ability to create immutable TVK backups, so that they cannot be deleted from until the user defined retention period is over. Trilio interfaces with the backup storage target's locking and versioning feature to provide immutability. Immutability is set at the backupPlan level, so users have the granular capability to specify which applications need immutability support. Currently, any S3-compatible device (AWS S3, MinIO, Wasabi etc.) that allows locking is supported.

## Pre-requisite 

### Create S3 bucket for immutable target
Trilio supports the creation of immutable backups on S3 devices that support object locking and versioning capabilities. Steps below describes creating S3 bucket on AWS with object locking enabled for creating immutable target.

In order for TrilioVault to support immutability the S3 bucket must have:
- Object Locking enabled
- Versioning enabled (this is generally enabled automatically with Object Locking if not a separate switch/flag)
- Default retenion period set for the bucket

1. Navigate to Amazon S3 - Create Bucket and provide details such as
   - Bucket name
   - AWS Region
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-1.png" width="1200"/>

   - Bucket Versioning - select Enable

   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-2.png" width="1200"/>
   
   - Advanced Settings
     - Object Lock - select Enable
     - Check the checkbox for the aknowledgment 

   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-3.png" width="1200"/>

   click **Create Bucket**
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-4.png" width="1200"/>
   
2. Once created, navigate to Amazon S3 - buckets and select the bucket just created
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-4a.png" width="1200"/>
   
   Go to properties
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-4b.png" width="1200"/>
   
   Edit Object Lock and enable **Default Retention**. 
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-4c.png" width="1200"/>
   
   Select **Default retention mode** and **Default retention period**. Click **Save Changes**.
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-4d.png" width="1200"/>
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-4e.png" width="1200"/>
   
## Steps to create Immutable Target
1. Create a secret for the Amazon S3 bucket created in above step
   ```
   $ cat <<EOF | kubectl apply -f -
   apiVersion: v1
   kind: Secret
   metadata:
     name: demo-s3-secret
   type: Opaque
   stringData:
     accessKey: AKIAS5B35DGFSTY7T55D
     secretKey: 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
   EOF
   ```
2. Log in to the **Management Console** of _Triliovault for Kubernetes_. 
3. Go to **Backup & Recovery** tab and click on **Targets**. It displays a list if existing targets. Click on **Create New**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/target/crd-target-1.png" width="1200"/>
4. On **CREATE TARGET** page, provide below details 
   - Namespace 
   - Threshold Capacity
   - Vendor Details
   - Select NFS or ObejctStore. In this example, we selected ObjectStore
   - For ObjectStore
     - Vendor
     - BucketName
     - Region
     - URL
     - Object Locking (Optional - used for immutable target)
     - Object store credentials
       - Select the Credential Secret created earlier from the list
     - Enable Browsing (optional - to enable target browsing)

   Once done, click on **Continue**
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/target/crd-target-2.png" width="1200"/>
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/target/crd-target-3.png" width="1200"/>
   
5. On this page, provide a name for the target and click **Create Target**. 
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/target/crd-target-4.png" width="1200"/>
   
6. This shows the confirmation that target is created and the list of targets is displayed. The initial status of the target is _InProgress_. It takes a while to become _Available_.
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/target/crd-target-5.png" width="1200"/>
7. Once the target becomes _Available_, target browsing can be enabled using the _Browsing_ toggle.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/target/crd-target-6.png" width="1200"/>

## Steps to create Scheduling and Retention Policies

## Steps to create immutable backup
TrilioVault for Kubernetes provides the ability to create immutable backups at the application level. Once the backup is taken and stored on an immutable target, it can not be altered (overwritten/deleted) until the retention period set through TVK is up.
After immutable target and retention policy are set, user needs to create a backup. Once the backup is taken and stored on an immutable target, it can not be altered (overwritten/deleted) until the retention period set through TVK is up. 

1. Log in to the **Management Console** of _Triliovault for Kubernetes_. 
2. Click on **Backup & Recovery** tab. It displays the primary cluster and a list of namespaces.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/encryption1.png" width="1200"/>
3. To create a backup, select a namespace from the list and click on **Actions** -> **Create Backup**
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/encryption2.png" width="1200"/>
4. In the **CREATE NEW BACKUP** wizard, a list of Backup Plans are displayed. If there are no pre-existing Backup Plans, click on **Create New** 
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/encryption3.png" width="1200"/>
5. This will lead to **CREATE NEW BACKUPPLAN** wizard. Provide mandatory parameters like Name, Target. To enable encrypion, select _Encryption Secret_ as created in the pre-requisite above. Provide optional parameters such as Scheduling policy, Retention policy and click on **Next**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/encryption4.png" width="1200"/>  
6. On this page, options are listed for Resource Selection - Included Resources and Excluded Resources. In this example, these are not used. Click on **Create**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/encryption5.png" width="1200"/>
7. This will take you back to **CREATE BACKUP** wizard. The newly created Backupplan is listed here. It takes some time for the backupplan to become active. Once it is active, select the backupplan. It provides the details of the backupplan upon expanion. Click **Continue**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/encryption6.png" width="1200"/>
8. Provide the name of the Backup and click **Create Backup**.
    <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/encryption7.png" width="1200"/>  
9. This shows the confirmation that backup is started and **STATUS LOG** is displayed. Once completed, this displays the status. In case of failure, this displays the stage where the failure occurs with specific errors.
    <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/encryption8.png" width="1200"/>
    
    <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/encryption9.png" width="1200"/>
    
10. This completes the creation of encrypted backup.

## Delete immutable bckup from TVK
Immutable backups once created can only be deleted via the Trilio retention process. Manual deletes from the TVK management console or from the target device itself will not remove the backup. In case of manual deletion from the TVK management console, the backup will be soft-deleted (only the backup reference will be removed from the cluster).

## Steps to restore the immutable backup

Lets take a look at how to restore the encrypted backup. The backup can be restored to different namespace on the same cluster or a different cluster.

1. Navigate to **Backupplans**. This displays a list of backupplans. Select the backupplan for restore and click **Actions** -> **Backup & Restore Summary**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/encryption10.png" width="1200"/>
2. This shows **MONITORING** page where backup and restore details are provided. To restore, select a backup and click **Restore**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/encryption11.png" width="1200"/>
3. This shows **RESTORE BACKUP**. Provide a name for the restore. An _Encryption Secret_ is auto-selected. Select the "Restore Flags". Click **Next**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/encryption12.png" width="1200"/>
4. On this page **Advanced** tab provides options for "Tranform Components", "Exclude Resources" and "Hook Configuration". These are not selected in this example. Click **Save** to proceed.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/encryption13.png" width="1200"/>
9. This shows the confirmation that restore is started and **STATUS LOG** is displayed. Once completed, this displays the status. In case of failure, this displays the stage where the failure occurs with specific errors.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/encryption14.png" width="1200"/>
10. This completes the restore of the encrypted backup.

## How to recover backups if deleted from S3 bucket
