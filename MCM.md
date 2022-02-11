# Multi Cluster Management
TrilioVault for Kubernetes ships with a user interface that simplifies management of the solution. It simplifies it by enabling simple click-driven workflows to backup, recover, and migrate applications between Kubernetes clusters (or namespaces) that have _TrilioVault for Kubernetes_ installed in them.

On the **Cluster Management** (Home) page, users can add and navigate clusters. TVK supports authentication via KubeConfig files and via Dex, which is an identity service IDP plugin for other identity providers. Below is the step-by-step guide to add clusters using LDAP and OIDC (Google SSO) authentications. Refer to documentation [here](https://docs.trilio.io/kubernetes/management-console/user-interface/ui-authentication) for other authentication methods.

## Adding a New Cluster

1. Log in to the **Management Console** of _Triliovault for Kubernetes_. 
2. The **Cluster Management** (Home) tab is displayed by default, which displays a list of TV-enabled clusters.
![image](https://github.com/[sachin-trilio]/[HowTos]/blob/[media]/MCM-image-1.png?raw=true)
3. Click **New Cluster** to add a new clusters.
4. Provide the requested details, like **Name**, **URL**, and **Distribution Type**. 
5. The new cluster can be authenticated using kubeconfig or LDAP/OIDC authentication. As shown below, the configured authentication method will be displayed, e.g. 
   - Name: democluster1
   - URL: https://default.k8s-tvk.com/login
   - Distribution Type: GKE
 
   ![image](https://user-images.githubusercontent.com/39940531/152997613-f0a0e9df-640f-4a7b-9e70-1275d20bba36.png)

6. Continue to sign-in via LDAP.
![image](https://user-images.githubusercontent.com/39940531/152997641-44e81e1b-c298-4104-81a9-7edc0f71acf6.png)
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
