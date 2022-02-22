# Storage Class Migration

**Storage Classes (SC)**
- StorageClass abstracts underlying storage provider.
- StorageClass provisions PV dynamically, when PVC claims it. StorageClass allows dynamically provision volumes for an incoming claim.
- StorageClass is used in conjunction with PVC that allow Pods to dynamically request a new storage.
- StorageClass use provisioners that are specific to the storage platform or cloud provider to give Kubernetes access to the physical storage.
- Each storage backend has own provisioner. Storage Backend is defined in the StorageClass component via provisioner attribute.

**Configure Storage Class**
Storage Class example using CSI storage from GKE
```
$ cat <<EOF | kubectl apply -f -
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: gcp-pd-sc
  annotations:
    storageclass.kubernetes.io/is-default-class: "true"
provisioner: pd.csi.storage.gke.io
reclaimPolicy: Delete
volumeBindingMode: Immediate
EOF
```
**List the Storage Classes on the cluster**
```
$ kubectl get sc
NAME                  PROVISIONER             RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
gcp-pd-sc (default)   pd.csi.storage.gke.io   Delete          Immediate              false                  1d
premium-rwo           pd.csi.storage.gke.io   Delete          WaitForFirstConsumer   true                   1d
standard              kubernetes.io/gce-pd    Delete          Immediate              true                   1d
standard-rwo          pd.csi.storage.gke.io   Delete          WaitForFirstConsumer   true                   1d
```

## Storage Class migration while restoring a backup to different cluster
Lets take a look at how to migrate storage class used for the PVC in the backed up application to a different storage class on different cluster. TVK provides "transforms" feature which allows changing resources during restore. 
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

## Storage Class migration while restoring a backup to same cluster

Lets take a look at how to migrate storage class used for the PVC in the backed up application to a different storage class on same cluster. 

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
