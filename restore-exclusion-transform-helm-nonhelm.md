# Pre-requisite
1. Backups created from helm-based backupplan - refer [example](https://docs.trilio.io/kubernetes/architecture/apis-and-command-line-reference/custom-resource-definitions-application-1/triliovault-crds#type-helm-example-1-single-helm-release)
2. Backups created from non-helm-based backupplan - refer [example](https://docs.trilio.io/kubernetes/architecture/apis-and-command-line-reference/custom-resource-definitions-application-1/triliovault-crds#type-namespace-example-1)

# Restore with Exclusions
TrilioVault for Kubernetes provides the capability to exclude resource while doing restores. TVK allows users to exclude metadata and data components as part of a restore.  The user can use Group, Version, Kind, Object mapping to point to a resource and exclude it from a restore operation. 

## Restore with exclusions for Helm-based backup - Not Supported

## Restore with exclusions for Non-helm-based backup

Lets take a look at how to restore with exclusions for Non-helm-based backup. The backup can be restored to different namespace on the same cluster or a different cluster.

1. Navigate to **Backupplans**. This displays a list of backupplans. Select the backupplan for restore and click **Actions** -> **Backup & Restore Summary**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/restore-exclusion-transform/exclusion-nonhelm-1.png" width="1200"/>
2. This shows **MONITORING** page where backup and restore details are provided. To restore, select a backup and click **Restore**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/restore-exclusion-transform/exclusion-nonhelm-2.png" width="1200"/>
3. This shows **RESTORE BACKUP**. Provide a name for the restore. Select the "Restore Flags". Click **Next**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/restore-exclusion-transform/exclusion-nonhelm-3.png" width="1200"/>
4. On this page **Advanced** tab provides options for "Tranform Components", "Exclude Resources" and "Hook Configuration". Click **Exclude Resources** tab.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/restore-exclusion-transform/exclusion-nonhelm-4.png" width="1200"/>
5. This shows **Exclude Resources** tab. Click on **Add Exclude Resources**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/restore-exclusion-transform/exclusion-nonhelm-5.png" width="1200"/>
6. On this page **ADD EXCLUDE RESOURCES**, a list of resources is displayed. Select a resource from this list.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/restore-exclusion-transform/exclusion-nonhelm-6.png" width="1200"/>
7. On this page **SELECT OBJECT TO EXCLUDE**, list of objects are displayed. Select objects to be excluded and click **Add**. 
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/restore-exclusion-transform/exclusion-nonhelm-7.png" width="1200"/>
8. This gets back to page **ADD EXCLUDE RESOURCES**. Repeat above step to select the resources and objects to be excluded.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/restore-exclusion-transform/exclusion-nonhelm-8.png" width="1200"/>
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/restore-exclusion-transform/exclusion-nonhelm-9.png" width="1200"/>
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/restore-exclusion-transform/exclusion-nonhelm-10.png" width="1200"/>
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/restore-exclusion-transform/exclusion-nonhelm-11.png" width="1200"/>
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/restore-exclusion-transform/exclusion-nonhelm-12.png" width="1200"/>
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/restore-exclusion-transform/exclusion-nonhelm-13.png" width="1200"/>
  
9. Once all the resources and objects are selected, click **Add**.   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/restore-exclusion-transform/exclusion-nonhelm-14.png" width="1200"/>
10. This gets back to **Advanced** tab. Click **Save** to start the restore.
    <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/restore-exclusion-transform/exclusion-nonhelm-15.png" width="1200"/>
11. This shows the confirmation that restore is started and **STATUS LOG** is displayed. Once completed, this displays the status. In case of failure, this displays the stage where the failure occurs with specific errors.
    <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/restore-exclusion-transform/exclusion-nonhelm-16.png" width="1200"/>
12. This completes the restore.

# Restore with Transforms
TrilioVault for Kubernetes provides "transforms" feature which allows changing resources while doing restores. 

## Restore with transforms for Helm-based backup

Lets take a look at how to restore with transforms for Helm-based backup. The backup can be restored to different namespace on the same cluster or a different cluster. Helm is backed up and restored/deployed as a package so we can't modify individual resources like custom. Helm provides values.yaml to modify package deployment which is leveraged in transformation.

1. Navigate to **Backupplans**. This displays a list of backupplans. Select the backupplan for restore and click **Actions** -> **Backup & Restore Summary**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/sc-migration/sc-migration-12.png" width="1200"/>
2. This shows **MONITORING** page where backup and restore details are provided. To restore, select a backup and click **Restore**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/sc-migration/sc-migration-13.png" width="1200"/>
3. This shows **RESTORE BACKUP**. Provide a name for the restore and select the "Restore Flags". Click on **Next** to proceed.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/sc-migration/sc-migration-14.png" width="1200"/>
4. **Advanced** tab provides options for "Tranform Components", "Exclude Resources" and "Hook Configuration". By default, "Transform Components" is displayed. Click on **Add Transform Components** to start the restore.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/sc-migration/sc-migration-15.png" width="1200"/>
5. On the **ADD TRANSFORM COMPONENT** page, select **Transformation Type** as **Custom** and select the **pvc** resource.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/sc-migration/sc-migration-16.png" width="1200"/> 
6. On this page, the pvc name is displayed in **Objects**. Provide **Transform Name**, provide details for **JSON Patches** and click **Apply**.
   ```
   Operation - Replace
   Path      - /spec/storageClassName
   Value     - "standard"
   ```
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/sc-migration/sc-migration-17.png" width="1200"/> 
7. This gets back to **ADD TRANSFORM COMPONENT** page. The details of the transform component is displayed. Click on **Add**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/sc-migration/sc-migration-18.png" width="1200"/>
8. This gets back to **RESTORE BACKUP** page **Advanced** tab. The details of the transform component is displayed. Click on **Save**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/sc-migration/sc-migration-19.png" width="1200"/>
9. This page shows the confirmation that restore is started and **STATUS LOG** is displayed. Once completed, this displays the status. In case of failure, this displays the stage where the failure occurs with specific errors.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/sc-migration/sc-migration-11.png" width="1200"/>
10. This completes the restore with replacing the storageclass of the PVC with new storageclass on the same cluster. 

## Restore with transforms for Non-helm-based backup

Lets take a look at how to restore the encrypted backup. The backup can be restored to different namespace on the same cluster or a different cluster.

1. Navigate to **Backupplans**. This displays a list of backupplans. Select the backupplan for restore and click **Actions** -> **Backup & Restore Summary**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/sc-migration/sc-migration-12.png" width="1200"/>
2. This shows **MONITORING** page where backup and restore details are provided. To restore, select a backup and click **Restore**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/sc-migration/sc-migration-13.png" width="1200"/>
3. This shows **RESTORE BACKUP**. Provide a name for the restore and select the "Restore Flags". Click on **Next** to proceed.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/sc-migration/sc-migration-14.png" width="1200"/>
4. **Advanced** tab provides options for "Tranform Components", "Exclude Resources" and "Hook Configuration". By default, "Transform Components" is displayed. Click on **Add Transform Components** to start the restore.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/sc-migration/sc-migration-15.png" width="1200"/>
5. On the **ADD TRANSFORM COMPONENT** page, select **Transformation Type** as **Custom** and select the **pvc** resource.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/sc-migration/sc-migration-16.png" width="1200"/> 
6. On this page, the pvc name is displayed in **Objects**. Provide **Transform Name**, provide details for **JSON Patches** and click **Apply**.
   ```
   Operation - Replace
   Path      - /spec/storageClassName
   Value     - "standard"
   ```
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/sc-migration/sc-migration-17.png" width="1200"/> 
7. This gets back to **ADD TRANSFORM COMPONENT** page. The details of the transform component is displayed. Click on **Add**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/sc-migration/sc-migration-18.png" width="1200"/>
8. This gets back to **RESTORE BACKUP** page **Advanced** tab. The details of the transform component is displayed. Click on **Save**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/sc-migration/sc-migration-19.png" width="1200"/>
9. This page shows the confirmation that restore is started and **STATUS LOG** is displayed. Once completed, this displays the status. In case of failure, this displays the stage where the failure occurs with specific errors.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/sc-migration/sc-migration-11.png" width="1200"/>
10. This completes the restore with replacing the storageclass of the PVC with new storageclass on the same cluster. 