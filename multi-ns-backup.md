# Multiple Namespaces Backup and Restore
TrilioVault for Kubernetes supports backup and restore of multiple namespaces from a kubernetes cluster. The user at times needs to protect more than 1 namespace. This feature helps the user to create a backup plan for multiple namespaces. The backus can be scheduled or created on demand. This feature also makes it easy for the user to restore some or all of the namespaces that are backed up. 

## Steps to create multiple namespaces backup

1. Log in to the **Management Console** of _Triliovault for Kubernetes_. 
2. Click on **Backup & Recovery** tab. It displays the primary cluster and a lit of namespaces.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/multi-ns-backup-1.png" width="1200"/>
3. To create a multi namespace backup, select more than 1 namespace from the list and click on **Bulk Actions** -> **Create Backup**
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/multi-ns-backup-2.png" width="1200"/>
4. In the **CREATE NEW BACKUP** wizard, a list of Backup Plans are displayed. If there are no pre-existing Backup Plans, click on **Create New** 
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/multi-ns-backup-3.png" width="1200"/>
5. This will lead to **CREATE MULTI-NAMESPACE BACKUPPLAN** wizard. Provide mandatory parameters like Target and optional parameters such as Encryption, Scheduling policy, Retention policy and click on **Next**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/multi-ns-backup-4.png" width="1200"/>  
6. On this page, options are listed for Resource Selection - Included Resources and Excluded Resources. In this example, these are not used. Click on **Next**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/multi-ns-backup-5.png" width="1200"/>
7. On this page Namespace Configuration, selected namespace are listed. To add namespace specific configuration like hooks, exclude resources and include resources, click on "Add Configuration" in front of each namespace. An option to "Add namespace" is also listed. Once completed, click on **Create**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/multi-ns-backup-6.png" width="1200"/>
8. Provide the name of the Backupplan and click **Done**
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/multi-ns-backup-7.png" width="1200"/>
9. This will take you back to **CREATE BACKUP** wizard. The newly created Backupplan is listed here. It takes some time for the backupplan to become active. Once it is active, select the backupplan. It provides the details of the backupplan upon expanion. Click **Continue**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/multi-ns-backup-8.png" width="1200"/>
10. Provide the name of the Backup and click **Create Backup**.
    <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/multi-ns-backup-9.png" width="1200"/>  
11. This shows the confirmation that backup is started and **STATUS LOG** is displayed. Once completed, this displays the status. In case of failure, this displays the stage where the failure occurs with specific errors.
    <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/multi-ns-backup-10.png" width="1200"/>
12. This completes the creation of backup of multiple namespaces. All the namespaces selected are not protected.

## Steps to restore the multiple namespaces backup

Now that the namespaces are protected, lets take a look at how to restore these namespaces. The namespaces can be restored to different namespace on the same cluster or a different cluster.

1. Navigate to **Backupplans**. This displays a list of backupplans. Select the backupplan for restore and click **Actions** -> **Backup & Restore Summary**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/multi-ns-backup-11.png" width="1200"/>
2. This shows **MONITORING** page where backup and restore details are provided. To restore, select a backup and click **Restore**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/multi-ns-backup-12.png" width="1200"/>
3. This shows **RESTORE MULTI-NS BACKUP**. By default,**Global Configuration** tab is selected. Provide a name for the restore and select the "Restore Flags". Next, click on **Namespace Configuration** tab.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/multi-ns-backup-13.png" width="1200"/>
4. On this page **CONFIGURE AND EXCLUDE NAMESPACES**, a list of _backued up namespace_ is provided. Click on **Add configuration** in front of each namespace to provide a configuration like "Restore Namespace". This steps needs to be repeated for each namespace where additional configuration needs to be provided.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/multi-ns-backup-14.png" width="1200"/>
5. This takes you to **ADD NAMESPACE CONFIGURATION** page. Provide "Restore Namespace" and "Restore Flags" under Basic tab. 
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/multi-ns-backup-15.png" width="1200"/>
6. **Advanced** tab provides options for "Tranform Components", "Exclude Resources" and "Hook Configuration". These are not selected in this example. Click **Save** to proceed.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/multi-ns-backup-16.png" width="1200"/>
7. Repeat the steps 8 and 9 for other namespaces.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/multi-ns-backup-17.png" width="1200"/>
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/multi-ns-backup-18.png" width="1200"/>
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/multi-ns-backup-19.png" width="1200"/>
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/multi-ns-backup-20.png" width="1200"/>

8. Once the namespace configurations are saved, click **Create** to start the restore
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/multi-ns-backup-21.png" width="1200"/>
9. This shows the confirmation that restore is started and **STATUS LOG** is displayed. Once completed, this displays the status. In case of failure, this displays the stage where the failure occurs with specific errors.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/multi-ns-backup-22.png" width="1200"/>
10. This completes the restore of multi-ns backup. All the namespaces are restored to new namespaces on the same cluster.

