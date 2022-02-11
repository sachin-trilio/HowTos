# Multi Cluster Management
TrilioVault for Kubernetes ships with a user interface that simplifies management of the solution. It simplifies it by enabling simple click-driven workflows to backup, recover, and migrate applications between Kubernetes clusters (or namespaces) that have _TrilioVault for Kubernetes_ installed in them.

On the **Cluster Management** (Home) page, users can add and navigate clusters. TVK supports authentication via KubeConfig files and via Dex, which is an identity service IDP plugin for other identity providers. Below is the step-by-step guide to add clusters using LDAP and OIDC (Google SSO) authentications. Refer to documentation [here](https://docs.trilio.io/kubernetes/management-console/user-interface/ui-authentication) for other authentication methods.

## Adding a New Cluster

1. Log in to the **Management Console** of _Triliovault for Kubernetes_. 
2. The **Cluster Management** (Home) tab is displayed by default, which displays a list of TV-enabled clusters.
![image](https://github.com/sachin-trilio/HowTos/blob/main/media/MCM-image-1.png)
3. Click **New Cluster** to add a new cluster.
4. Provide the requested details, like **Name**, **URL**, and **Distribution Type**. For example:
   1. Name: democluster1
   2. URL: https://default.k8s-tvk.com/login
   3. Distribution Type: GKE
      ![image](https://github.com/sachin-trilio/HowTos/blob/main/media/MCM-image-2.png)
5. The new cluster can be authenticated using Kubeconfig or LDAP/OIDC authentication. 
   1. For Kubecobnfig, click on this field to browse your local system to select a previously configured Kubeconfig file.
   2. To sign-in via LDAP, first make sure that the LDAP option is displayed. If it is, click **Sign-in via LDAP** and then sign in with your LDAP username and password and click **Login**.
      ![image](https://github.com/sachin-trilio/HowTos/blob/main/media/MCM-image-3.png)
7. Following successful sign-in, a message displays confirming that the cluster has been added.
![image](https://user-images.githubusercontent.com/39940531/152997672-5d094647-7cf3-4065-8fac-d21c77e011d0.png)
8. Check that the new cluster (_democluster1_) is now listed on the UI.
![image](https://user-images.githubusercontent.com/39940531/152997698-d801455f-b074-4314-a3b5-ac042a60d3d6.png)

## Adding Another Cluster
1. To add another cluster, repeat steps 1-5 from the previous section (Adding a New Cluster).
2. Continue to sign-in via Google (OIDC).
![image](https://user-images.githubusercontent.com/39940531/152997863-8e293023-d794-4e02-ba1a-c20964dcf00b.png)
3. Following successful sign-in, a message displays confirming that the cluster has been added.
![image](https://user-images.githubusercontent.com/39940531/152997884-c8ae844c-e61f-42f9-ba9b-12bc43e64be0.png)
4. Check that the new cluster (_democluster2_) is now listed on the UI.
5. From the **Cluster Management** (Home) page, click your new cluster name to use it for further operations.
![image](https://user-images.githubusercontent.com/39940531/152998410-dbe457e2-610b-4e2e-8dae-d11f949d823d.png)
