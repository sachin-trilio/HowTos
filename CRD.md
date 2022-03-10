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

## Policies
TrilioVault provides a Policy Custom Resource Definition through which policies for scheduling and retention.

### Scheduling Policy
A scheduling policy can be created to automate the capture of applications within a Kubernetes system on a periodic basis. TVK enables users to create a scheduling policy with multiple cron strings defined within it. Each Cron string creates an associated cron job within the Kubernetes system and as a result daily, weekly, yearly, etc. policies can be created independently of each other within the same policy CR.

### Steps to create Scheduling Policy
1. Log in to the **Management Console** of _Triliovault for Kubernetes_. 
2. Go to **Backup & Recovery** tab and click on **Policies**. By default, it displays **Scheduling Policies** tab with a list of pre-defined and user created scheduling policies. Click on **Create New**.
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
2. Go to **Backup & Recovery** tab and click on **Policies**. Click on **Retention Policies** tab. It displays a list of pre-defined and user created retention policies. Click on **Create New**.
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
Hooks enable injecting commands into pods/containers before and after a backup via pre/post commands. Hooks enable taking application consistent backups and extending backup workflows. Hooks enable users to run application specific commands while performing a backup/restore. Quiescing and Unquiescing commands can be run to take application consistent backups/restore.
Note: Hook should be created in the same namespace as that of BackupPlan referencing it resides.

### Steps to create hooks
1. Log in to the **Management Console** of _Triliovault for Kubernetes_. 
2. Go to **Backup & Recovery** tab and click on **Hooks**. It displays a list of existing hooks. Click on **Create New**.
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
The BackupPlan CRD specifies the backup job. The specification includes the backup schedule, backup target and the resources to backup. TrilioVault supports three types of resources to backup and an BackupPlan CR may include combination of these resources.
This BackupPlan CR defines a set of resources to backup. Resources can be defined in the form of Helm release, Operators or just bare k8s api resources.
TrilioVault supports backup of the following:
- Helm releases
- Operator-based application instances
- Label-based selection of resources
- Namespaces

### Backup Plan - Namespace

1. Log in to the **Management Console** of _Triliovault for Kubernetes_. 
2. Go to **Backup & Recovery** tab and click on **Backupplans**. It displays a list of existing Backupplans. Click on **Create New** and select **Single-namespace** to create a namespace based Backupplan.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-ns/backupplan-1.png" width="1200"/>
3. This leads to **CREATE NEW BACKUPPLAN** wizard. In **Step 1: Configuration** tab, provide folliwing details.
   - Namespace - select the namespace from the drop-down list
   - Name - Provide Backupplan name
   - Target - Select a Backup Target from the drop-dwon list
   - Encryption Secret (Optional) - select to enable encryption for the backup
   - HOOK CONFIGURATION (Hooks enable users to run application specific commands while performing a backup/restore. Quiescing and Unquiescing commands can be run to take application consistent backups/restore. Hooks are optional and multiple Hooks can be defined per backupplan.)
     - Add hook (Optional - if selected, continue. Else, proceed to next step)
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-ns/backupplan-2.png" width="1200"/>

   - This leads to **ADD HOOK** wizard. Provide below details
     - Hook - Select hook from a drop-down list
     - POD SELECTOR (Provide one of the below to select a pod for executing the hook commands)
       - Label Set
       - Regex
     - CONTAINER REGEX
       - Regex
    Click on **Add**
    
    <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-ns/backupplan-3.png" width="1200"/>

   - This leads back to **HOOK CONFIGURAION** wizard. Provide below details
     - Quiescing Mode - Sequential / Parallel
     - Pod Ready Wait (sec)
     - HOOKS - lists down hook selected above
 
    User can add more hooks. 
    
    <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-ns/backupplan-4.png" width="1200"/>

4. Scroll down to **SCHEDULING POLICY** part. This is optional. Scheduling policy allows users to specify two schedules, one for Full Backup and another for incremental backup.
Every full backup starts a new backup chain. Provide provide folliwing details.
   - Full Backup - Select a policy from a drop-down list
   - Incremental Backup - Select a policy from a drop-down list

   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-ns/backupplan-5a.png" width="1200"/>  

   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-ns/backupplan-5.png" width="1200"/>  
   
   If selected policy is from a different namespace, it displays a warning that a copy of the policy will be created in the current namespace. Click **Continue**
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-ns/backupplan-6.png" width="1200"/>
   
5. Scroll down to **Retention Policy** part. This is optional. Retention policy allows the user to specify interval wise number of backups to retain. User can specify multiple intervals. Select a policy from a drop-down list. Click **Next**.

   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-ns/backupplan-7.png" width="1200"/>  
   
6. On this page **Step 2:Resource Selector**, options are listed for Resource Selection - Included Resources and Excluded Resources. In this example, these are not used. Click on **Create**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-ns/backupplan-8.png" width="1200"/>
7. This shows the confirmation that Backup Plan is created.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-ns/backupplan-9.png" width="1200"/>
8. The newly created Backup Plan takes few seconds to become _Available_. Once it is in _Available_ state, user can proceed with Backups.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-ns/backupplan-10.png" width="1200"/>

### Backup Plan - Application (Label/Helm/Operator together)

1. Log in to the **Management Console** of _Triliovault for Kubernetes_. 
2. Go to **Backup & Recovery** tab and click on **Backupplans**. It displays a list of existing Backupplans. Click on **Create New** and select **Application** to create an application based Backupplan.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-app/backupplan-1.png" width="1200"/>
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-app/backupplan-2.png" width="1200"/>
   
3. This leads to **CREATE NEW BACKUPPLAN** wizard. In **Step 1: Configuration** tab, provide folliwing details.
   - Namespace - select the namespace from the drop-down list
   - Name - Provide Backupplan name
   - Target - Select a Backup Target from the drop-dwon list

   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-app/backupplan-3.png" width="1200"/>
   
   If selected target is from a different namespace, it displays a warning that a copy of the target will be created in the current namespace. Click **Continue**
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-app/backupplan-4.png" width="1200"/>

   - Encryption Secret (Optional) - select to enable encryption for the backup
   - HOOK CONFIGURATION (Hooks enable users to run application specific commands while performing a backup/restore. Quiescing and Unquiescing commands can be run to take application consistent backups/restore. Hooks are optional and multiple Hooks can be defined per backupplan.)
     - Add hook (Optional - if selected, continue. Else, proceed to next step)
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-app/backupplan-5.png" width="1200"/>

   - This leads to **ADD HOOK** wizard. Provide below details
     - Hook - Select hook from a drop-down list
     - POD SELECTOR (Provide one of the below to select a pod for executing the hook commands)
       - Label Set
       - Regex
     - CONTAINER REGEX
       - Regex
    Click on **Add Label Set**
    
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-app/backupplan-6.png" width="1200"/>
    
   - This leads to **ADD LABEL SET** page.  By default, it displays **MatchLabels** tab.
     - Match Label - Select a label from a drop-dwon list.
   Click on **Apply**
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-app/backupplan-7.png" width="1200"/>
   
   This displays **ADDED LABLES**. More labels can be added by repeating above step. Once done, click **Add**.
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-app/backupplan-8.png" width="1200"/>
   
   This leads back to **ADD HOOK** page. Provide other details if not done already.
     - Hook - Select hook from a drop-down list
     - POD SELECTOR (Provide one of the below to select a pod for executing the hook commands)
       - Label Set
       - Regex
     - CONTAINER REGEX
       - Regex
    Click on **Add**
    
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-app/backupplan-9.png" width="1200"/> 

   - This leads back to **HOOK CONFIGURAION** wizard. Provide below details
     - Quiescing Mode - Sequential / Parallel
     - Pod Ready Wait (sec)
     - HOOKS - lists down hook selected above
 
    User can add more hooks. 
    
    <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-app/backupplan-10.png" width="1200"/>

4. Scroll down to **SCHEDULING POLICY** part. This is optional. Scheduling policy allows users to specify two schedules, one for Full Backup and another for incremental backup.
Every full backup starts a new backup chain. Provide provide folliwing details.
   - Full Backup - Select a policy from a drop-down list
   - Incremental Backup - Select a policy from a drop-down list

   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-app/backupplan-11.png" width="1200"/>  

   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-app/backupplan-12.png" width="1200"/>  
   
   If selected policy is from a different namespace, it displays a warning that a copy of the policy will be created in the current namespace. Click **Continue**
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-app/backupplan-13.png" width="1200"/>
   
5. Scroll down to **Retention Policy** part. This is optional. Retention policy allows the user to specify interval wise number of backups to retain. User can specify multiple intervals. Select a policy from a drop-down list. Click **Next**.

   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-app/backupplan-14.png" width="1200"/>  
   
6. On this page **Step 2:Component Details**, by default **Operator** tab is displayed. When TVK performs backup of an Operator based application, the Operator resources, custom resources, as well as the application resources are backed up. The UI pre-fills components for CRs/ORs/ARs and the user has the ability to edit and add/remove more items as they deem necessary.
   Click on **Add Operator**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-app/backupplan-15.png" width="1200"/>
7. Enter **Operator Name or ID** and click **Apply**

   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-app/backupplan-16.png" width="1200"/>
   
   This shows a list of components that user can add/update. Once done, click **Create**
   - Custom Resources
   - Operator Resources
   - Application Resources
   - Excluded Resources

   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-app/backupplan-17.png" width="1200"/>

8. This leads back to page **Step 2:Component Details**. Click on **Helm Release** tab and **Add Helm Release** to add a helm chart release in the Backup Plan.

   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-app/backupplan-18.png" width="1200"/>
   
   Select a Helm Release from a drop-down list and click **Apply**.
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-app/backupplan-19.png" width="1200"/>
   
   User can add more helm release by clicking **Add Another** and following above step. Once done, click **Create**.
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-app/backupplan-20.png" width="1200"/>
   
9. This leads back to page **Step 2:Component Details**. Click on **Custom** tab. For **Included Resources**, click on **Add Resources** 

   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-app/backupplan-21.png" width="1200"/>
   
   This leads to **LABEL SET** page.  By default, it displays **MatchLabels** tab.
     - Match Label - Select a label from a drop-dwon list.
   Click on **Apply**
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-app/backupplan-22.png" width="1200"/>
   
   This displays **ADDED LABLES**. More labels can be added by repeating above step. Repeating similar steps, **Resources** can be added. Once done, click **Add**.
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-app/backupplan-23.png" width="1200"/>
   
10. This leads back to page **Step 2:Component Details**. It displays added **Lable Sets** and **Resources** under **Included Resources**. Same way **Excluded Resources** can be added. Once done, click **Create**.

   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-app/backupplan-24.png" width="1200"/>

11. This shows the confirmation that Backup Plan is created.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-app/backupplan-25.png" width="1200"/>
12. The newly created Backup Plan takes few seconds to become _Available_. Once it is in _Available_ state, user can proceed with Backups.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backupplan-app/backupplan-26.png" width="1200"/>
   
## Backup
The Backup CRD takes a backup of the resources specified in the BackupPlan spec. It takes either a Full backup or Incremental Backup. The first backup of the Application will always be a Full backup even if the user specifies their backup type as Incremental.
Note: BackupPlan and Backup CR should be created in same namespace.
Incremental backup includes all the YAML files and delta changes to PV data. Incremental backups are not complete by themselves and relies on all the previous incremental backup and the full backup.

### Backup - Namespace
1. Log in to the **Management Console** of _Triliovault for Kubernetes_. 
2. Go to **Backup & Recovery** tab and click on **Backupplans**. It displays a list of existing Backupplans. For creating namespace Backup, select a namespace based Backup Plan and click on **Actions** -> **Create Backup**
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backup-ns/backup-1.png" width="1200"/>
3. In the **CREATE NEW BACKUP** wizard, selected Backup Plan is listed. The details can be checked by expanding it. Provide the name of the Backup and click **Create**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backup-ns/backup-2.png" width="1200"/>  
4. This shows the confirmation that backup is started and **STATUS LOG** is displayed. Once completed, this displays the status. In case of failure, this displays the stage where the failure occurs with specific errors.
    <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backup-ns/backup-3.png" width="1200"/>
    
    <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backup-ns/backup-4.png" width="1200"/>
    
5. This completes the creation of namespace based backup.

### Backup - Application (Label/Helm/Operator together)
1. Log in to the **Management Console** of _Triliovault for Kubernetes_. 
2. Go to **Backup & Recovery** tab and click on **Backupplans**. It displays a list of existing Backupplans. For creating application Backup, select an application based Backup Plan and click on **Actions** -> **Create Backup**
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backup-app/backup-1.png" width="1200"/>
3. In the **CREATE NEW BACKUP** wizard, selected Backup Plan is listed. The details can be checked by expanding it. Provide the name of the Backup and click **Create**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backup-app/backup-2.png" width="1200"/>  
4. This shows the confirmation that backup is started and **STATUS LOG** is displayed. Once completed, this displays the status. In case of failure, this displays the stage where the failure occurs with specific errors.
    <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backup-app/backup-3.png" width="1200"/>
    
    <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/backup-app/backup-4.png" width="1200"/>
    
5. This completes the creation of application based backup.

## Restore
The Restore CRD specifies the backup that resources need to be restored from. Resources can be restored to the same namespace or a different namespace. If a backup target with existing backups are created to a different cluster, those backups can be restored to the cluster. A migration or disaster recovery use case can be implemented using this functionality.

1. Log in to the **Management Console** of _Triliovault for Kubernetes_. 
2. Go to **Backup & Recovery** tab and click on **Backupplans**. This displays a list of backupplans. Select the backupplan for restore and click **Actions** -> **Backup & Restore Summary**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/restore/restore-1.png" width="1200"/>
2. This shows **MONITORING** page where backup and restore details are provided. To restore, select a backup and click **Restore**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/restore/restore-2.png" width="1200"/>
3. This shows **RESTORE BACKUP**. Provide a name for the restore. Select a restore namespace from a drop-down list. Select the "Restore Flags". Click **Next**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/restore/restore-3.png" width="1200"/>
4. On this page **Advanced** tab provides options for "Tranform Components", "Exclude Resources" and "Hook Configuration". These are not selected in this example. Click **Save** to proceed.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/restore/restore-4.png" width="1200"/>
5. This shows the confirmation that restore is started and **STATUS LOG** is displayed. 
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/restore/restore-5.png" width="1200"/>
6. Once completed, this displays the status. In case of failure, this displays the stage where the failure occurs with specific errors.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/CRD/restore/restore-6.png" width="1200"/>
7. This completes the restore of the backup.
