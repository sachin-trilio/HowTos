# Encrypting Backup and Restore
TrilioVault for Kubernetes provides the capability to encrypt backups. Encryption of the backups provides protection from malicious users. TVK encrypts the backup data at the application level using an encryption key which can only be restored with the same encryption key. Encryption is enabled in the BackupPlan and different keys will be created for each backupPlan. Encryption keys are stored as Kubernetes secrets, and integration with Vault will be provided soon. Trilio leverages the LUKS encryption format to protect data. LUKS is extremely flexible and secure providing a range of cipher suites. 

## Pre-requisite - Create an encryption secret
1. Create a base64 encrypted key.
   ```
   echo -n "_Key text_" | base64
   ```
   Example - 
   ```
   $ echo -n myencryptionkey | base64
   bXllbmNyeXB0aW9ua2V5
   $
   ```
2. Create a secret in the namespace which needs to be backed up.
   ```
   $ cat <<EOF | kubectl apply -f -
   apiVersion: v1
   kind: Secret
   metadata:
     name: encryption-secret
   type: Opaque
   data:
     encryptKey: bXllbmNyeXB0aW9ua2V5
   EOF
   ```

## Steps to create encrypted backup

1. Log in to the **Management Console** of _Triliovault for Kubernetes_. 
2. Click on **Backup & Recovery** tab. It displays the primary cluster and a list of namespaces.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/EnryptionBackup/encryption1.png" width="1200"/>
3. To create a backup, select a namespace from the list and click on **Actions** -> **Create Backup**
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/EnryptionBackup/encryption2.png" width="1200"/>
4. In the **CREATE NEW BACKUP** wizard, a list of Backup Plans are displayed. If there are no pre-existing Backup Plans, click on **Create New** 
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/EnryptionBackup/encryption3.png" width="1200"/>
5. This will lead to **CREATE NEW BACKUPPLAN** wizard. Provide mandatory parameters like Name, Target. To enable encrypion, select _Encryption Secret_ as created in the pre-requisite above. Provide optional parameters such as Scheduling policy, Retention policy and click on **Next**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/EnryptionBackup/encryption4.png" width="1200"/>  
6. On this page, options are listed for Resource Selection - Included Resources and Excluded Resources. In this example, these are not used. Click on **Create**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/EnryptionBackup/encryption5.png" width="1200"/>
7. This will take you back to **CREATE BACKUP** wizard. The newly created Backupplan is listed here. It takes some time for the backupplan to become active. Once it is active, select the backupplan. It provides the details of the backupplan upon expanion. Click **Continue**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/EnryptionBackup/encryption6.png" width="1200"/>
8. Provide the name of the Backup and click **Create Backup**.
    <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/EnryptionBackup/encryption7.png" width="1200"/>  
9. This shows the confirmation that backup is started and **STATUS LOG** is displayed. Once completed, this displays the status. In case of failure, this displays the stage where the failure occurs with specific errors.
    <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/EnryptionBackup/encryption8.png" width="1200"/>
    
    <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/EnryptionBackup/encryption9.png" width="1200"/>
    
10. This completes the creation of encrypted backup.

## Steps to restore the encrypted backup

Lets take a look at how to restore the encrypted backup. The backup can be restored to different namespace on the same cluster or a different cluster.

1. Navigate to **Backupplans**. This displays a list of backupplans. Select the backupplan for restore and click **Actions** -> **Backup & Restore Summary**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/EnryptionBackup/encryption10.png" width="1200"/>
2. This shows **MONITORING** page where backup and restore details are provided. To restore, select a backup and click **Restore**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/EnryptionBackup/encryption11.png" width="1200"/>
3. This shows **RESTORE BACKUP**. Provide a name for the restore. An _Encryption Secret_ is auto-selected. Select the "Restore Flags". Click **Next**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/EnryptionBackup/encryption12.png" width="1200"/>
4. On this page **Advanced** tab provides options for "Tranform Components", "Exclude Resources" and "Hook Configuration". These are not selected in this example. Click **Save** to proceed.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/EnryptionBackup/encryption13.png" width="1200"/>
9. This shows the confirmation that restore is started and **STATUS LOG** is displayed. Once completed, this displays the status. In case of failure, this displays the stage where the failure occurs with specific errors.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/EnryptionBackup/encryption14.png" width="1200"/>
10. This completes the restore of the encrypted backup.
