# Disaster Recovery Plan

## Overview
Whether you are running one app or multiple apps, every organization needs a strategy to recover from a disaster quickly and restore business operations. Newer-age paradigms like GitOps provide a neat way to deploy/re-deploy applications, however, when you deal with mission critical applications, you require a turnkey solution like TrilioVault that can be leveraged to redeploy all apps from their last captured running state versus working with the application team to deploy their applications at another site. This problem gets amplified when you deal with applications and services at scale. Once business continuity is restored, GitOps can connect to the DR cluster and sync accordingly.

Furthermore, stateful applications also need data volumes to connect to the metadata configuration, so a solution like TVK that can capture application consistent PIT metadata and data for DR perspective is needed.

With these challenges in mind, TVK provides a DRPlan feature which allows users to restore multiple applications or namespaces as part of a single workflow to restore business operations. This feature is provided as a simple click-driven solution through the management console for constructing the DR plan and executing it within a destination cluster.
Users can now create and execute various DRPlans for different tiers of applications based on criticality from multiple targets or backup locations. The DRPlan is created and saved at the destination cluster - which means that the plan can be executed even when the source cluster is not available. While marketed as a disaster recovery workflow, this same workflow can be used to for application migration and test/dev purposes as well (though these will have their own workflows in the future).

## DR Plan Highlights
1. DR Plans are created from the resource management page of a particular cluster.
2. No connection or dependency to the source cluster is needed.
3. First page of the DRPlan focuses on selecting namespaces and apps from various targets, whereas the second page of the DRPlan focuses on massaging the application restores so that they can fit and function well within the target environment.
   - Transforms can be created once and applied to all other applications being restored in the DRPlan.
4. The individual backups are restored in parallel at the same time. If any delay is required between the restores etc. that can be achieved by using transforms and adding an 'init' container to the application pod.
5. Users can monitor the status of the DRPlan from the resource management tab itself and can look at the status of individual restores from the restore overview page.

## Steps to create and execute a DR Plan
Lets take a look at how to create a Disaster Recovery Plan and execute it. 
1. Log in to the **Management Console** of _Triliovault for Kubernetes_. 
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/DR-plan/dr-plan-1.png" width="1200"/>
2. Click on **Disaster Recovery**. It displays a list of DR Plans configured on the cluster. Click **Create New**. 
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/DR-plan/dr-plan-2.png" width="1200"/>
3. On this page, provide details to create a new DR Plan. Provide **Disaster Recovery Plan Name**. Select the **Target**. After selecting the target, it populates a list of Backup Plans.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/DR-plan/dr-plan-3.png" width="1200"/>
4. Select 1 or more backup plans from the list. This will appear in the **SELECTED BACKUPPLANS**. Click on **Next**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/DR-plan/dr-plan-4.png" width="1200"/>
5. On this page, **SELECTED BACKUPPLANS** are listed. Select a BackupPlan. In the **RESTORE CONFIGURATION** panel, the backup name is selected. This can be changed by licking on **change** in front of **Backup name**. Provide **Name** for the restore, **Restore Namespace** and select the "Restore Flags". Other panels provide options for "Tranform Components", "Exclude Resources" and "Hook". In this example these are not selected. 
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/DR-plan/dr-plan-5.png" width="1200"/>
6. Repeat above step for all the Backup Plans from **SELECTED BACKUPPLANS** panel. Once done, click on **Execute**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/DR-plan/dr-plan-6.png" width="1200"/>
   
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/DR-plan/dr-plan-7.png" width="1200"/>
   
7. On this page **DISASTER RECOVERY PLAN EXECUTION**, it asks for the confirmation. Click **Continue**.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/DR-plan/dr-plan-8.png" width="1200"/>
8. This page shows the confirmation that DR Plan is started and **STATUS LOG** is displayed. Once completed, this displays the status. In case of failure, this displays the stage where the failure occurs with specific errors.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/DR-plan/dr-plan-9.png" width="1200"/>
9. Navigate back to **Disaster Recovery**. This lists the DR Plans executed. Click on it to check the status.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/DR-plan/dr-plan-10.png" width="1200"/>
10. This completes the creation and execution of the Disaster Recover Plan. 
