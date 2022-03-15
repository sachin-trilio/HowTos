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
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-5.png" width="1200"/>
4. On **CREATE TARGET** page, provide below details 
   - Namespace 
   - Threshold Capacity
   - Vendor Details
   - Select ObejctStore
   - For ObjectStore
     - Vendor
     - BucketName
     - Region

   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-6.png" width="1200"/>

     - URL
     - Object Locking - Enable to create immutable target)
     - Object store credentials
       - Select the Credential Secret created earlier from the list
     - Enable Browsing (optional - to enable target browsing)

   Once done, click on **Continue**
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-7.png" width="1200"/>
   
5. On this page, provide a name for the target and click **Create Target**. 
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-8.png" width="1200"/>
   
6. This shows the confirmation that target is created and the list of targets is displayed. The initial status of the target is _InProgress_. It takes a while to become _Available_.
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-9.png" width="1200"/>

## Steps to create Scheduling and Retention Policies
1. Log in to the **Management Console** of _Triliovault for Kubernetes_. 
2. Go to **Backup & Recovery** tab and click on **Policies**. By default, it displays **Scheduling Policies** tab with a list of pre-defined and user created scheduling policies. Click on **Create New**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-10.png" width="1200"/>
3. On **CREATE SCHEDULING POLICY** page, provide below details 
   - Namespace 
   - Scheduling Policy Name
   - Add Interval (user can add multiple intervals and if selected, multiple backups will be created as per the interval.) Click on one of the inervals.
     - Hourly
     - Daily
     - Weekly
     - Monthly
     - Yearly

   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-11.png" width="1200"/>
   
4. For the selected **Interval**, provide **Schedule Time**. User can add more schedules. Once done, click on **Create**
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-12.png" width="1200"/>
5. This shows the confirmation that Scheduling Policy is created and the list of scheduling policies is displayed.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-13.png" width="1200"/>
6. Click on **Retention Policies** tab. It displays a list of pre-defined and user created retention policies. Click on **Create New**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-14.png" width="1200"/>
7. On **CREATE RETENTION POLICY** page, provide below details 
   - Namespace 
   - Retention Policy Name
   - Add Interval (user can add multiple intervals) Click on add another.
     - Latest - No. of backups
     - Daily - Provide the time
     - Weekly - Select a day of the week
     - Monthly - Select a day of the month
     - Yearly - Select a month of the year

   User can add more intervals. Once done, click on **Create**
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-15.png" width="1200"/>
   
8. This shows the confirmation that Retention Policy is created and the list of retention policies is displayed.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-16.png" width="1200"/>

## Steps to create immutable backup plan
After immutable target and retention policy are set, user needs to create a backupPlan. When an Immutable target is created and used within a backupPlan, a scheduling policy and a retention policy will be required for the backupPlan. TrilioVault validates that the default retention on the bucket and the retention specified by the user are in alignment. If not, Triliovault will fail the creation of the backupPlan with appropriate error messages.

1. Log in to the **Management Console** of _Triliovault for Kubernetes_. 
2. Go to **Backup & Recovery** tab and click on **Backupplans**. It displays a list of existing Backupplans. Click on **Create New** and select **Single-namespace** to create a namespace based Backupplan.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-17.png" width="1200"/>
3. This leads to **CREATE NEW BACKUPPLAN** wizard. In **Step 1: Configuration** tab, provide folliwing details.
   - Namespace - select the namespace from the drop-down list
   - Name - Provide Backupplan name
   - Target - Select Immutable Backup Target from the drop-dwon list
   - Encryption Secret (Optional) - select to enable encryption for the backup
   - HOOK CONFIGURATION (Optional)
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-18.png" width="1200"/>

4. Scroll down to **SCHEDULING POLICY** part. This is needed for immutable backuPlan. Scheduling policy allows users to specify two schedules, one for Full Backup and another for incremental backup.
Every full backup starts a new backup chain. Provide provide folliwing details.
   - Full Backup - Select a policy from a drop-down list
   - Incremental Backup - Select a policy from a drop-down list

   Scroll down to **Retention Policy** part. This is needed for immutable backupPlan. Retention policy allows the user to specify interval wise number of backups to retain. User can specify multiple intervals. Select a policy from a drop-down list. Click **Next**.

   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-19.png" width="1200"/>  
   
5. On this page **Step 2:Resource Selector**, options are listed for Resource Selection - Included Resources and Excluded Resources. In this example, these are not used. Click on **Create**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-20.png" width="1200"/>
6. This shows the confirmation that Backup Plan is created. The newly created Backup Plan takes few seconds to become _Available_. Once it is in _Available_ state, user can proceed with Backups.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-21.png" width="1200"/>

## Steps to create immutable backup
TrilioVault for Kubernetes provides the ability to create immutable backups at the application level. Once the backup is taken and stored on an immutable target, it can not be altered (overwritten/deleted) until the retention period set through TVK is up.
After immutable target and retention policy are set and backuipPlan is created, user needs to create a backup. Once the backup is taken and stored on an immutable target, it can not be altered (overwritten/deleted) until the retention period set through TVK is up. 

1. Log in to the **Management Console** of _Triliovault for Kubernetes_. 
2. Go to **Backup & Recovery** tab and click on **Backupplans**. It displays a list of existing Backupplans. For creating immutable Backup, select Immutable Backup Plan created in above step and click on **Actions** -> **Create Backup**
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-22.png" width="1200"/>
3. In the **CREATE NEW BACKUP** wizard, selected Backup Plan is listed. The details can be checked by expanding it. Provide the name of the Backup and click **Create**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-23.png" width="1200"/>  
4. This shows the confirmation that backup is started and **STATUS LOG** is displayed. Once completed, this displays the status. In case of failure, this displays the stage where the failure occurs with specific errors.
    <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-24.png" width="1200"/>
    
    <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-backup-25.png" width="1200"/>
    
5. This completes the creation of immutable backup.

## Delete immutable backup from TVK
Immutable backups once created can only be deleted via the Trilio retention process. Manual deletes from the TVK management console or from the target device itself will not remove the backup. In case of manual deletion from the TVK management console, the backup will be soft-deleted (only the backup reference will be removed from the cluster).

Lets take  look at how to delete a backup from the TVK management console.

1. Navigate to **Backupplans**. This displays a list of backupplans. Select the backupplan for restore and click **Actions** -> **Backup & Restore Summary**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-delete-1.png" width="1200"/>
2. This shows **MONITORING** page where backup and restore details are provided. Click on the **...*** at the right hand corner.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-delete-2.png" width="1200"/>
3. This shows **view YAML** option. Click it.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-delete-3.png" width="1200"/>
4. This leads to TVK Resource YAML page. In the **Backup** YAML, find the field **location**. Note this down as it indicates the location of the backup inside the S3 bucket. Once done, click **Close**. 
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-delete-4.png" width="1200"/>
5. This brings back the **MONITORING** page. Click on **Delete** to delete the backup. It asks for a confirmation. Click **Yes**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-delete-5.png" width="1200"/>
6. This shows the confirmation that the backup is deleted.   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-delete-6.png" width="1200"/>
7. Now, navigate back to **Amazon S3 - Buckets**. Click on the immutable bucket.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-delete-7.png" width="1200"/>
8. Navigate to the objects inside the bucket and find the **location** of the backup. Go inside the folder to check if the backup taken is still available in the Amazon S3 bucket.   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-delete-8.png" width="1200"/>
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-delete-9.png" width="1200"/>
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-delete-10.png" width="1200"/>  
9. This indicates that deleting the backup from the TVK management console actualy performs a soft-delete. The entry of the backup is deleted from the TVK management console. However, the backup objects are still available in the S3 bucket.

## Steps to restore the immutable backup

Lets take a look at how to restore the immutable backup. The backup can be restored to different namespace on the same cluster or a different cluster.

1. Log in to the **Management Console** of _Triliovault for Kubernetes_. 
2. Click on **Backup & Recovery** tab and select **Targets**. It displays the list of targets configured on the cluster.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-restore-1.png" width="1200"/>
3. Select the immutable **Target** where the immutable backup was taken. Please note that **Browsing** must be enabled for the Target. Click on **Actions** for the selected target and click on **Launch Browser**. 
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-restore-2.png" width="1200"/>
4. This opens the **Target Browser** and shows a list of BackupPlans. 
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-restore-3.png" width="1200"/>
5. Select the appropriate BackupPlan, in this case the immutable backupPlan created earlier. It provides the details of the immutable backup. To restore, click **Restore**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-restore-4.png" width="1200"/>
6. This shows **RESTORE BACKUP**. Provide a name for the restore, select a namespace for the restore and select the "Restore Flags". Click on **Next** to proceed.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-restore-5.png" width="1200"/>
7. **Advanced** tab provides options for "Tranform Components", "Exclude Resources" and "Hook Configuration". These are not selected in this example. Click **Save** to start the restore.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-restore-6.png" width="1200"/>
8. This page shows the confirmation that restore is started and **STATUS LOG** is displayed. Once completed, this displays the status. In case of failure, this displays the stage where the failure occurs with specific errors.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-restore-7.png" width="1200"/>
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/immutable-restore-8.png" width="1200"/>
   
9. This completes the restore of an immutable backup. All the application are restored to the selected namespace on the cluster.

## How to recover backups if deleted from S3 bucket
If the backup objects are accidently deleted from the Amazon S3 bucket, these can be recovered as the bucket has object locking and version enabled. Steps to recover the backups if it is deleted from S3 bucket 

1. Navigate to **Amazon S3 - Buckets** and click the immutable bucket. Navigate to the objects inside the bucket and find the **location** of the backup as noted in the step above. Click **Delete** to imitate deletion of the backup folder.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/S3-object-delete-1.png" width="1200"/>
2. Provide confirmations for the deletion and click **Delete objects**.   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/S3-object-delete-2.png" width="1200"/>
3. This provides confirmation that objects are deleted.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/S3-object-delete-3.png" width="1200"/>
4. Navigate back to the Amazon S3 bucket and enable **Show versions**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/S3-object-delete-4.png" width="1200"/>
5. Search the backup folder inside the bucket and go inside the folder.   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/S3-object-delete-5.png" width="1200"/> 
6. Inside the folder, **Delete marker** objects are listed. Delete these **Delete marker** objects to recover the backup objects.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/S3-object-delete-6.png" width="1200"/>
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/S3-object-delete-7.png" width="1200"/>   
   
7. Navigate back to the bucket. It will show that the backup folder is now recovered and can be used for restore.

   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/S3-object-delete-8.png" width="1200"/>
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/immutable-backup/S3-object-delete-9.png" width="1200"/>
