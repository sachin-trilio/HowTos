# Deploying Triliovault For Kubernetes with Openshift ACM Policies 

## Table of Contents

- [Introduction](#Introduction)
  - [Prerequisites](#Prerequisites)
- [Install RHACM](#Install_RHACM)
- [Deploy Triliovault for Kubernetes (TVK) policies](#Deploy_Triliovault_for_Kubernetes_TVK_policies)
  - [Install TVK using policy](#Install_TVK_using_policy)
  - [Protect a namespace in the cluster using the TVK backup creation policy](#Protect_a_namespace_in_the_cluster_using_the_TVK_backup_creation_policy)
  - [Protect multiple namespaces in any cluster using the TVK backup creation policy](#Protect_multiple_namespaces_in_any_cluster_using_the_TVK_backup_creation_policy)
- [Conclusion](#Conclusion)
- [References](#References)

## Introduction
Organizations are moving towards Kubernetes as an operating environment, and protecting the data is paramount. It’s their top-most priority to protect the business-critical data and set up a business continuity plan in case of a disaster. A cloud native backup and DR solution is the need of the hour, and the answer is Triliovault for Kubernetes.

TrilioVault for Kubernetes is a cloud-native, application-centric data protection platform designed to support the scale, performance, and mobility requirements of Kubernetes container environments across any public or hybrid cloud environment. It offers backup and recovery of the entire application, including data, metadata, and Kubernetes objects. It is protected and can be restored from any point-in-time.

Red Hat Advanced Cluster Management for Kubernetes is a management solution designed to help organizations extend and scale Red Hat OpenShift, the leading enterprise Kubernetes platform. It enables management consistency across hybrid cloud deployments, including on-premises and public clouds.

Integration between Triliovault for Kubernetes (TVK) and Red Hat Advanced Cluster Management for Kubernetes (RHACM) provides an automated solution to protect containers, Kubernetes, and cloud infrastructure using the policy framework. This powerful framework allows users to govern multiple clusters in the data center using policies. The TVK policies enable the users to protect their Openshift clusters and ensure continued protection for existing and new clusters.

This blog will walk us through how to deploy the TVK policies on RHACM. It’s an efficient way to protect your cloud workloads.

### Prerequisites
Pre-requisites are as follows:

- Red Hat Openshift Kubernetes (version >=4.8) clusters up and running
- Pre-requisites for using Triliovault for Kubernetes - https://docs.trilio.io/kubernetes/getting-started-3/getting-started#prerequisites-for-tvk
- CSI Driver with snapshot capability - https://docs.trilio.io/kubernetes/appendix/csi-drivers
- VolumeSnapshot CRDs - https://docs.trilio.io/kubernetes/appendix/csi-drivers/installing-volumesnapshot-crds

## Install RHACM
Red Hat Advanced Cluster Management can be used to comply with enterprise and industry standards for aspects such as security and regulatory compliance, resiliency, and software engineering. Deviation from defined values for such standards represents a configuration drift, which can be detected using the built-in configuration policy controller of RHACM.

Install Red Hat Advanced Cluster Management (RHACM >= 2.5) as outlined in the https://access.redhat.com/documentation/en-us/red_hat_advanced_cluster_management_for_kubernetes/2.6/html/install/index.

## Deploy Triliovault for Kubernetes (TVK) policies
In RHACM Policy Collection under the community section, the following 3 TVK policies are available.

| Policy | Description | Prerequisites |
| ------ | ----------- | ------------- |
| Policy to install Triliovault for Kubernetes Operator | Use this policy to install Triliovault for Kubernetes Operator and a trial license on Openshift clusters with label "protected-by=triliovault" | Requires OpenShift 4.8 or later. Needs CSI Driver with snapshot capabilities, storageClass and volumeSnapshotClass. For more information, refer documentation |
| Policy to create namespace based backup using Triliovault for Kubernetes | Use this policy to create namespace based backup using Triliovault for Kubernetes on Openshift clusters with label "protected-by=triliovault" | Requires OpenShift 4.8 or later. Note: Triliovault for Kubernetes must be installed to use this policy. See the Policy to install Triliovault for Kubernetes Operator. On the hub cluster, create a secret "aws-s3-secret" with S3 credentials and a configmap "aws-s3-configmap" with S3 bucket name, region name & thresholdCapacity in the namespace “default” where this policy is created (details given in the policy). For more information, refer documentation |
| Policy to create namespace based backup using Triliovault for Kubernetes and kyverno template | Use this policy to create namespace based backup using Triliovault for Kubernetes and kyverno template on Openshift clusters with label "protected-by=triliovault". It creates backup of the namespaces having a label "protected-by=tvk-ns-backup" | Requires OpenShift 4.8 or later. Note: Kyverno controller must be installed to use the kyverno policy. See the Policy to install Kyverno. Triliovault for Kubernetes must be installed to use this policy. See the Policy to install Triliovault for Kubernetes Operator. On the hub cluster, create a secret "aws-s3-secret" with S3 credentials and a configmap "aws-s3-configmap" with S3 bucket name, region name & thresholdCapacity in the namespace “default” where this policy is created (details given in the policy). For more information, refer documentation |

Let’s start with the policy to install Triliovault for Kubernetes (TVK).

### Install TVK using policy
We are going to use the policy to install TVK. This policy installs the latest TVK operator on any “Openshift” cluster with a label protected-by=triliovault.

For demo purposes, there are two OCP clusters; one of them runs RHACM and is the “hub” cluster. We can see that when we go to the “Cluster lifecycle” section of RHACM:

![](/acm/image-20220927-104207.png)

An important note here: One of the clusters was prepared with the label protected-by=triliovault

This RHACM has not yet created any TVK policies, so let’s start with our first example.

To start, let’s go to the bottom left group on the RHACM start page, and we’ll see the UI for Governance and risk (also called: The policy engine):
(./image-20220927-104347.png)

We click on “Create Policy”:
(/image-20220927-104532.png)

By default, we see the YAML code on the right side, which makes it also easy for us to import the above-mentioned first policy. Let’s go to the GitHub page, click on “Raw” for the policy YAML, and just copy the YAML code from GitHub into the YAML section of RHACM. Note: Before pasting into RHACM, clear the YAML section there. Typically you do a <ctrl>-a <ctrl>-c in the GitHub Window, and a <ctrl>-a <ctrl>-v in the RHACM window. After you paste the policy into that YAML-Edit Window in RHACM, you should have the following:
(/image-20220927-104911.png)
  
In the last line of the policy code, in the “PlacementRule” section, we see that this policy should be used on all clusters with labels vendor=OpenShift and protected-by=triliovault. This policy will be deployed on all “Openshift” clusters with user-defined label protected-by=triliovault. Before we can press the “Create” button, we still need to select a namespace in which this policy shall be executed. This is for internal organization reasons only; it does NOT affect the results of the policy engine itself. So, on the left-hand side, we can select the “default” namespace or any other namespace available on the hub cluster. The user can create some specific policy-engine namespaces in advance to be able to group them more efficiently. Also, we will not yet select the “Enforce if supported” button.

Before we create the policy, let’s again check the list of installed operators on the cluster itself in its OpenShift UI:
<image-20220927-105207.png> 
  
We see that TVK is not installed.

So, let’s create the policy by clicking on the “Submit” button in the “Create policy” dialog in RHACM.

We are forwarded to a screen, which after a couple of moments, looks like this:

TrilioVault for K8S > Deploying Triliovault For Kubernetes with Openshift ACM Policies > (image-20220927-105419.png)
We see that RHACM detected that the policy shall be used on 1 cluster and that the policy is NOT adhered to in this cluster. Therefore, we have one policy violation. We can click on the policy name to get a more detailed overview, and there we select the “Results” Tab:

TrilioVault for K8S > Deploying Triliovault For Kubernetes with Openshift ACM Policies > (image-20220927-105553.png)
We see: The required operator elements are missing, which is why the policy failed.

If we go back to the policy overview - Policies, where the policy is listed, we see three dots at the end of the line. If we click on those, we get a popup box in which we can select an action to the policy:

TrilioVault for K8S > Deploying Triliovault For Kubernetes with Openshift ACM Policies > (image-20220927-105724.png)
Click "Enforce" to confirm that action in the next popup box.

TrilioVault for K8S > Deploying Triliovault For Kubernetes with Openshift ACM Policies > (image-20220927-105952.png)
It takes a few minutes for enforcement of the Policy. When we again check the details of the policy, we see:

TrilioVault for K8S > Deploying Triliovault For Kubernetes with Openshift ACM Policies > (image-20220927-111254.png)
And we can confirm in our cluster with the protected-by=triliovault label that the operator has been installed:

TrilioVault for K8S > Deploying Triliovault For Kubernetes with Openshift ACM Policies > (image-20220927-111420.png)
This concludes how to install Triliovault for Kubernetes (TVK) on the clusters managed by ACM. This policy will also protect the new clusters if those are Openshift clusters with a label protected-by=triliovault.

### Protect a namespace in the cluster using the TVK backup creation policy
Now that the TVK is installed and a trial license created (as seen in the previous policy), we can start using the TVK. To get started with TVK in our environment, please ensure that all prerequisites are fulfilled as listed in the section above. Once done, we need to perform the following steps.

- Ceate a TVK Target - Location where backups will be stored.
- Create a backup plan - Details about the backup
- Create a namespace-based backup

Policy to create a namespace-based backup using Triliovault for Kubernetes helps to achieve the above steps for a single namespace. Let's take a look at applying this policy. We created a namespace, “MySQL,” with the MySQL application running for the demo.

```
# oc get all -n mysql
NAME                                                                  READY   STATUS      RESTARTS   AGE
pod/k8s-demo-app-mysql-78dbddc54f-dtpd6                               1/1     Running     0          1d

NAME                         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
service/k8s-demo-app-mysql   ClusterIP   None         <none>        3306/TCP   1d

NAME                                 READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/k8s-demo-app-mysql   1/1     1            1           1d

NAME                                            DESIRED   CURRENT   READY   AGE
replicaset.apps/k8s-demo-app-mysql-78dbddc54f   1         1         1       1d
#
```
  
Let's quickly check the policy. As specified in the comments section, we need to create a secret “aws-s3-secret” and a configmap “aws-s3-configmap” in the namespace “default” where we are planning to place this policy on the hub cluster. These secret and configmap have AWS S3 details for target creation.

```
cat <<EOF | oc apply -f - -n default
apiVersion: v1
kind: Secret
metadata:
  name: aws-s3-secret
  namespace: default
type: Opaque
stringData:
  accessKey: "PROVIDE_ACCESS_KEY"
  secretKey: "PROVIDE_SECRET_KEY"
EOF
```
```
cat <<EOF | oc apply -f - -n default
apiVersion: v1
kind: ConfigMap
metadata:
  name: aws-s3-configmap
  namespace: default
data:
  bucketName: "PROVIDE_S3_BUCKET_NAME"
  region: "PROVIDE_REGION"
  thresholdCapacity: "100Gi"
EOF
```
  
Now, let’s click on “Create Policy.” By default, we see the YAML code on the right side, which makes it also easy for us to import the policy. Let’s go to the GitHub page, click on “Raw” for the policy YAML, and just copy the YAML code from GitHub into the YAML section of RHACM. Note: Before pasting into RHACM, clear the YAML section there. Typically you do a <ctrl>-a <ctrl>-c in the GitHub Window, and a <ctrl>-a <ctrl>-v in the RHACM window. After you paste the policy into that YAML-Edit Window in RHACM, you should have the following:

TrilioVault for K8S > Deploying Triliovault For Kubernetes with Openshift ACM Policies > (image-20220927-131018.png)
Let's quickly check the policy. As specified in the comments section, we need to replace the namespace “test” used in the policy with “mysql” for which we need to create the backup. It can be easily done in YAML-Edit Window in RHACM.

In the last line of the policy code, in the “PlacementRule” section, we see that this policy should be used on all clusters with labels vendor=OpenShift and protected-by=triliovault. This policy will be deployed on all “Openshift” clusters with user-defined label protected-by=triliovault. Before we can press the “Create” button, we still need to select a namespace in which this policy shall be executed. This is for internal organization reasons only; it does NOT affect the results of the policy engine itself. So, on the left-hand side, we can select the “default” namespace on the hub cluster.

TrilioVault for K8S > Deploying Triliovault For Kubernetes with Openshift ACM Policies > (image-20220927-133113.png)
Click “Submit” to create the policy.

TrilioVault for K8S > Deploying Triliovault For Kubernetes with Openshift ACM Policies > (image-20220927-141624.png)
We see that RHACM detected that the policy shall be used on 1 cluster and that the policy is NOT adhered to in this cluster. Therefore, we have one policy violation. We can click on the policy name to get a more detailed overview. If we go back to the policy overview - Policies, where the policy is listed, we see three dots at the end of the line. If we click on those, we get a popup box in which we can select an action “enforce” the policy:

TrilioVault for K8S > Deploying Triliovault For Kubernetes with Openshift ACM Policies > (image-20220927-141855.png)
Click " Enforce " to confirm that action in the next popup box.

TrilioVault for K8S > Deploying Triliovault For Kubernetes with Openshift ACM Policies > (image-20220927-142020.png)
It takes a few minutes for enforcement of the Policy. When we again check the details of the policy, we see:

TrilioVault for K8S > Deploying Triliovault For Kubernetes with Openshift ACM Policies > (image-20220928-153124.png)
As seen in the above screenshot, the policy created a secret that uses AWS S3 credentials, a target to store backup data in the specified S3 bucket, a BackupPlan with a daily backup schedule, and a first full backup of the namespace “mysql”. With this, the namespace “mysql” is protected, and a new backup will be taken daily.

We can also log in to TVK UI (refer https://docs.trilio.io/kubernetes/management-console-ui/accessing-the-ui#openshift-environments on Openshift) and sign in via Openshift.

TrilioVault for K8S > Deploying Triliovault For Kubernetes with Openshift ACM Policies > (image-20220929-074746.png)
Backups created for namespace “mysql” can be seen using Triliovault management UI as below:

TrilioVault for K8S > Deploying Triliovault For Kubernetes with Openshift ACM Policies > (image-20220929-075309.png)
This concludes how to create a namespace-based backup using TVK policy on the Openshift clusters with a label protected-by=triliovault managed by ACM.

However, in this policy, we need to update the namespace's name to create the backup manually. Also, it is expected that the same namespace is present on all the Openshift clusters with a label protected-by=triliovault.

It would be desirable to have a policy that will start protecting any namespace on any Openshift clusters matching the placement rule managed by ACM. Let’s take a look at a policy that does exactly that.

### Protect multiple namespaces in any cluster using the TVK backup creation policy
Before we begin, please ensure that all prerequisites are fulfilled as listed in the section above. Policy to create namespace based backup using Triliovault for Kubernetes and Kyverno template creates a backup of the namespaces having a label "protected-by=tvk-ns-backup" on all the Openshift clusters with label "protected-by=triliovault.” In addition to installing TVK, the Kyverno controller must be installed to use the kyverno policy. See the Policy to install Kyverno.

NOTE - Please grant Kyverno's service account additional privileges for the namespaces.

Once the prerequisites are met, this policy performs the following steps for each existing or newly created namespace, with the label "protected-by=tvk-ns-backup.”

- Create a TVK Target - Location where backups will be stored.
- Create a backup plan - Details about the backup
- Create a namespace-based backup

To start applying this policy, let's quickly check the policy. As specified in the comments section, we need to create a secret “aws-s3-secret” and a configmap “aws-s3-configmap” in the namespace “default” where we are planning to place this policy on the hub cluster. These secret and configmap have AWS S3 details for target creation.

```
cat <<EOF | oc apply -f - -n default
apiVersion: v1
kind: Secret
metadata:
  name: aws-s3-secret
  namespace: default
type: Opaque
stringData:
  accessKey: "PROVIDE_ACCESS_KEY"
  secretKey: "PROVIDE_SECRET_KEY"
EOF 
```
```
cat <<EOF | oc apply -f - -n default
apiVersion: v1
kind: ConfigMap
metadata:
  name: aws-s3-configmap
  namespace: default
data:
  bucketName: "PROVIDE_S3_BUCKET_NAME"
  region: "PROVIDE_REGION"
  thresholdCapacity: "100Gi"
EOF
```
Let’s go to the Governance → Policies section in the ACM. As we can see, install-tvk policy has been created and is compliant. That means TVK is installed and has a valid license.

TrilioVault for K8S > Deploying Triliovault For Kubernetes with Openshift ACM Policies > (image-20220929-083257.png)
Now, let’s click on “Create Policy.” By default, we see the YAML code on the right side, which makes it also easy for us to import the policy. Let’s go to the GitHub page, click on “Raw” for the policy YAML, and just copy the YAML code from GitHub into the YAML section of RHACM. Note: Before pasting into RHACM, clear the YAML section there. Typically you do a <ctrl>-a <ctrl>-c in the GitHub Window, and a <ctrl>-a <ctrl>-v in the RHACM window. After you paste the policy into that YAML-Edit Window in RHACM, you should have the following:

TrilioVault for K8S > Deploying Triliovault For Kubernetes with Openshift ACM Policies > (image-20220929-083731.png)
In the last line of the policy code, in the “PlacementRule” section, we see that this policy should be used on all clusters with labels vendor=OpenShift and protected-by=triliovault. This policy will be deployed on all “Openshift” clusters with user-defined label protected-by=triliovault. Before we can press the “Create” button, we still need to select a namespace in which this policy shall be executed. This is for internal organization reasons only; it does NOT affect the results of the policy engine itself. So, on the left-hand side, we can select the “default” namespace on the hub cluster.

TrilioVault for K8S > Deploying Triliovault For Kubernetes with Openshift ACM Policies > (image-20220929-084217.png)
Click “Submit” to create the policy.

TrilioVault for K8S > Deploying Triliovault For Kubernetes with Openshift ACM Policies > (image-20220929-084853.png)
We see that RHACM detected that the policy shall be used on 1 cluster and that the policy is NOT adhered to in this cluster. Therefore, we have one policy violation. We can click on the policy name to get a more detailed overview. If we go back to the policy overview - Policies, where the policy is listed, we see three dots at the end of the line. If we click on those, we get a popup box in which we can select an action “enforce” the policy:

TrilioVault for K8S > Deploying Triliovault For Kubernetes with Openshift ACM Policies > (image-20220929-085039.png)
Click " Enforce " to confirm that action in the next popup box.

TrilioVault for K8S > Deploying Triliovault For Kubernetes with Openshift ACM Policies > (image-20220929-085114.png)
It takes a few minutes for enforcement of the Policy. When we again check the details of the policy, we see:

TrilioVault for K8S > Deploying Triliovault For Kubernetes with Openshift ACM Policies > (image-20220929-085746.png)
The above screenshot shows that the policy created a Kyverno policy on the target cluster. This Kyverno policy monitors all the namespaces and creates a target to store backup data in the specified S3 bucket, a BackupPlan with a daily backup schedule, and a first full backup for all the namespaces with label protected-by=tvk-ns-backup. This is also applied to any new namespace created with the same label.

Let’s log in to TVK UI (refer https://docs.trilio.io/kubernetes/management-console-ui/accessing-the-ui#openshift-environments on Openshift) and sign in via Openshift.

TrilioVault for K8S > Deploying Triliovault For Kubernetes with Openshift ACM Policies > (image-20220929-090122.png)
As seen in the above screenshot, there are no backups in the “Monitoring” → Cluster Dashboard in TVK UI. This is because there are no namespaces in the target cluster with the label protected-by=tvk-ns-backup.

For the demo, we created a namespace, “postgres” with the PostgreSQL application running.

```
# oc get all -n postgres
NAME                  READY   STATUS    RESTARTS   AGE
pod/postgres-demo-0   1/1     Running   0          5m28s
pod/postgres-demo-1   1/1     Running   0          5m3s

NAME               TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/postgres   NodePort   172.30.17.37   <none>        5432:30992/TCP   5m29s

NAME                             READY   AGE
statefulset.apps/postgres-demo   2/2     5m29s
#
Let’s apply label protected-by=tvk-ns-backup to namespace “postgres”.

# oc label ns postgres protected-by=tvk-ns-backup
namespace/postgres labeled
#
```
  
It takes a few minutes for the Kyverno policy to come into effect. When we again check the details, we see:

```
# oc get target,backupplan,backup -n postgres
NAME                                                  TYPE          THRESHOLD CAPACITY   VENDOR   STATUS      BROWSING ENABLED
target.triliovault.trilio.io/tvk-postgres-s3-target   ObjectStore   100Gi                AWS      Available

NAME                                                       TARGET                   RETENTION POLICY   INCREMENTAL SCHEDULE   FULL BACKUP SCHEDULE           STATUS
backupplan.triliovault.trilio.io/tvk-postgres-backupplan   tvk-postgres-s3-target                                             trilio-daily-schedule-policy   Available

NAME                                               BACKUPPLAN                BACKUP TYPE   STATUS      DATA SIZE   CREATION TIME          START TIME             END TIME               PERCENTAGE COMPLETED   BACKUP SCOPE   DURATION
backup.triliovault.trilio.io/tvk-postgres-backup   tvk-postgres-backupplan   Full          Available   112328704   2022-09-29T10:15:03Z   2022-09-29T10:15:03Z   2022-09-29T10:23:22Z   100                    Namespace      8m19.479134026s
#
```
  
Let’s check “Monitoring” → Cluster Dashboard in TVK UI. Now we can see a backup created for namespace “postgres”.

TrilioVault for K8S > Deploying Triliovault For Kubernetes with Openshift ACM Policies > (image-20220929-112740.png)
This concludes how to create a namespace-based backup using Kyverno-based TVK policy for all the namespaces with label protected-by=tvk-ns-backup on the Openshift clusters with a label protected-by=triliovault managed by ACM. It keeps existing as well as new namespaces protected. So just apply with label protected-by=tvk-ns-backup for any namespace to start protecting it.

## Conclusion
TrilioVault for Kubernetes is a cloud-native, application-centric data protection platform designed to support the scale, performance, and mobility requirements of Kubernetes container environments across any public or hybrid cloud environment. It offers backup and recovery of the entire application, including data, metadata, and Kubernetes objects. It is protected and can be restored from any point-in-time.

- TrilioVault for Kubernetes (TVK) provides a wide range of helpful features, including:
- Native Kubernetes application
- Storage of metadata and all application resources to a specified target
- Support for Helm, Label, and Operators application deployment types and support for S3 or NFS-based backup targets
- Provision of application hooks to ensure data consistent backups
- ACM Policies to install TVK and create backups for namespaces.

TVK provides a strong platform for enterprise database backups. For more information on TVK, request a demo or download your free trial today.

## References
- https://www.opensourcerers.org/2021/08/23/rhacm-and-policies-an-introduction/
- https://cloud.redhat.com/blog/implement-policy-based-governance-using-configuration-management-of-red-hat-advanced-cluster-management-for-kubernetes
- https://access.redhat.com/documentation/en-us/red_hat_advanced_cluster_management_for_kubernetes/2.6/html/install/index
- https://docs.trilio.io/kubernetes/overview/readme

 
