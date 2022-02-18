# Restoring without access to source cluster
TrilioVault for Kubernetes supports restoring without access to source cluster. This feature allows user to restore any application on a cluster from an available backup. Pre-requisite is to configure the backup target where the application was backed up and stored on the cluster. The target browsing needs to be enabled. Refer this [link] (https://docs.trilio.io/kubernetes/management-console-ui/use-cases-with-trilio/restoring-backups/target-browsing#enabling-target-browsing) for the steps to do so. The user can then browse the backup target and find out the backupplan and backup that needs to be restored by following te steps below. 

## Steps to restore a backup without access to source cluster

1. Log in to the **Management Console** of _Triliovault for Kubernetes_. 
2. Click on **Backup & Recovery** tab and select **Targets**. It displays the list of targets configured on the cluster.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/restore-by-loc-2.png" width="1200"/>
3. Select the **Target** where the backup was taken. Please note that **Browsing** must be enabled for the Target. 
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/restore-by-loc-3.png" width="1200"/>
4. Click on **Actions** for the selected target and click on **Launch Browser**. 
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/restore-by-loc-4.png" width="1200"/>
5. This opens the **Target Browser** and shows a list of BackupPlans. 
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/restore-by-loc-5.png" width="1200"/>
6. Select the appropriate BackupPlan. It provides the details of the backup. To restore, click **Restore**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/restore-by-loc-6.png" width="1200"/>
6. This shows **RESTORE BACKUP**. Provide a name for the restore and select the "Restore Flags". Click on **Next** to proceed.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/restore-by-loc-7.png" width="1200"/>
4. **Advanced** tab provides options for "Tranform Components", "Exclude Resources" and "Hook Configuration". These are not selected in this example. Click **Save** to start the restore.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/restore-by-loc-8.png" width="1200"/>
5. This page shows the confirmation that restore is started and **STATUS LOG** is displayed. Once completed, this displays the status. In case of failure, this displays the stage where the failure occurs with specific errors.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/restore-by-loc-9.png" width="1200"/>
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/restore-by-loc-10.png" width="1200"/>
   
10. This completes the restore of a backup without access to source cluster. All the application are restored to the selected namespace on the cluster.
