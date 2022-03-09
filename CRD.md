# TrilioVault Custom Resources
Examples of TrilioVault Custom Resources

## Backup Target
The Backup Target CRD specifies the backup storage media. TrilioVault supports either AWS S3 compatible object storage or NFS. A user can configure multiple backup targets and choose the target when an Application CR is created by providing target name and the name of the namespace where it resides. The target credentials can be saved as a secret and refer to the target CR for better security reasons. All backups that are created for that Application will be saved on the backup target specified in Application CR spec. Once a backup target is chosen for an Application, it cannot be changed.

### Create secret with S3 credentials
1. Create a secret for AWS based S3 bucket and credentialSecret
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
3. Click on **Backup & Recovery** tab and click on **Targets**. It displays a list if existing targets. Click on **Create New**.
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

## Policies
TrilioVault provides a Policy Custom Resource Definition through which policies for scheduling and retention.

### Scheduling Policy
A scheduling policy can be created to automate the capture of applications within a Kubernetes system on a periodic basis. TVK enables users to create a scheduling policy with multiple cron strings defined within it. Each Cron string creates an associated cron job within the Kubernetes system and as a result daily, weekly, yearly, etc. policies can be created independently of each other within the same policy CR.

### Steps to create Scheduling Policy
1. Log in to the **Management Console** of _Triliovault for Kubernetes_. 
2. Click on **Backup & Recovery** tab and click on **Policies**. By default, it displays **Scheduling Policies** tab with a list of pre-defined and user created scheduling policies. Click on **Create New**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/Policies/policies-1.png" width="1200"/>
3. On **CREATE SCHEDULING POLICY** page, provide below details 
   - Namespace 
   - Scheduling Policy Name
   - Add Interval (user can add multiple intervals and if selected, multiple backups will be created as per the interval.) Click on one of the inervals.
     - Hourly
     - Daily
     - Weekly
     - Monthly
     - Yearly

   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/Policies/policies-2.png" width="1200"/>
   
4. For the selected **Interval**, provide **Schedule Time**. User can add more schedules. Once done, click on **Create**
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/Policies/policies-3.png" width="1200"/>
5. This shows the confirmation that Scheduling Policy is created and the list of scheduling policies is displayed.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/Policies/policies-4.png" width="1200"/>

### Retention Policy
The retention policy enables users to define the number of backups to retain and the cadence to delete backups as per compliance requirements. The retention policy CR provides a simple YAML specification to define the number of backups to retain in terms of days, weeks, months, years, latest etc.

### Steps to create Scheduling Policy
1. Log in to the **Management Console** of _Triliovault for Kubernetes_. 
2. Click on **Backup & Recovery** tab and click on **Policies**. Click on **Retention Policies** tab. It displays a list of pre-defined and user created retention policies. Click on **Create New**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/Policies/policies-5.png" width="1200"/>
3. On **CREATE RETENTION POLICY** page, provide below details 
   - Namespace 
   - Retention Policy Name
   - Add Interval (user can add multiple intervals) Click on add another.
     - Latest - No. of backups
     - Daily - Provide the time
     - Weekly - Select a day of the week
     - Monthly - Select a day of the month
     - Yearly - Select a month of the year

   User can add more intervals. Once done, click on **Create**
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/Policies/policies-6.png" width="1200"/>
   
5. This shows the confirmation that Retention Policy is created and the list of retention policies is displayed.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/Policies/policies-7.png" width="1200"/>

## Hooks
Hooks enable injecting commands into pods/containers before and after a backup via pre/post commands. Hooks enable taking application consistent backups and extending backup workflows.
Note: Hook should be created in the same namespace as that of BackupPlan referencing it resides.

### Steps to create hooks
1. Log in to the **Management Console** of _Triliovault for Kubernetes_. 
2. Click on **Hooks**. It displays a list of existing hooks. Click on **Create New**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/hooks/hooks-1.png" width="1200"/>
3. On **CREATE HOOK** page, provide below details 
   - Namespace 
   - Name
   - PRE (TVK provides capability to specify prestop commands to be executed against the application before backup operation. An optional timeout can also be specified for the commands)
     - Ignore Failure - Toggle True or False
     - Timeout(sec) (Optional)
     - Max Retry count (Optional)
     - Command - Add Command (At least one command needed, more than one command can be added)
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/hooks/hooks-2.png" width="1200"/>

   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/hooks/hooks-3.png" width="1200"/>

   - POST (TVK provides capability to specify poststart commands to be executed to quiesce the application before backup operation. An optional timeout can also be specified for the commands.)
     - Ignore Failure - Toggle True or False
     - Timeout(sec) (Optional)
     - Max Retry count (Optional)
     - Command - Add Command (At least one command needed, more than one command can be added)
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/hooks/hooks-4.png" width="1200"/>

   Once done, click on **Create**
   
5. This shows the confirmation that hook is created and the list of hooks is displayed.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/hooks/hooks-5.png" width="1200"/>
   
## Backup Plan

### Backup Plan - Namespace

### Backup Plan - Application (Label/Helm/Operator together)

## Backup

### Backup - Namespace

### Backup - Application (Label/Helm/Operator together)

## Restore

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

## Steps to restore the encrypted backup

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
