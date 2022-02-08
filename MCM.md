# Multi Cluster Management
TrilioVault for Kubernetes ships with a user interface that dramatically simplifies management of the solution by enabling simple click-driven workflows to backup, recover, migrate applications between Kubernetes clusters (or namespaces) that have TrilioVault for Kubernetes installed in them.

On the Home Page, users can add and navigate clusters. The TVK supports authentication via KubeConfig files and via Dex an IDP plugin for other Identity Providers. Below is the step-by-step guide to add clusters using LDAP and OIDC (Google SSO) authentications. For other authentication methods, refer https://docs.trilio.io/kubernetes/management-console/user-interface/ui-authentication.

1. Login to Management Console of Triliovault for Kubernetes. On the Home Page, by default, “Cluster Management” tab is selected and it displays the TV enabled clusters. 

![image](https://user-images.githubusercontent.com/39940531/152997569-c27aa9b6-222f-4e94-8773-b8bf76765791.png)

2. Click on “New Cluster” to add more clusters and provide details like Name, URL and Distribution Type. The new cluster can be authenticated using kubeconfig or LDAP/OIDC authentication. As shown below, the configured authentication method will be displayed.
e.g. 
- Name – democluster1
- URL - https://default.k8s-tvk.com/login 
- Distribution Type – GKE 

![image](https://user-images.githubusercontent.com/39940531/152997613-f0a0e9df-640f-4a7b-9e70-1275d20bba36.png)

3. Continue to sign-in via LDAP

![image](https://user-images.githubusercontent.com/39940531/152997641-44e81e1b-c298-4104-81a9-7edc0f71acf6.png)

4. On successful sign-in, a message will be displayed that the cluster is added.

![image](https://user-images.githubusercontent.com/39940531/152997672-5d094647-7cf3-4065-8fac-d21c77e011d0.png)

5. The new cluster “democluster1” is now listed on the UI.

![image](https://user-images.githubusercontent.com/39940531/152997698-d801455f-b074-4314-a3b5-ac042a60d3d6.png)

6. To add another cluster, click on “New Cluster” and provide details like Name, URL and Distribution Type. The new cluster can be authenticated using kubeconfig or LDAP/OIDC authentication. As shown below, the configured authentication method will be displayed.
e.g. 
- Name – democluster2
- URL - http://default.k8s-tvk.com/login  (A warning is displayed for insecure URL)
- Distribution Type – GKE 

![image](https://user-images.githubusercontent.com/39940531/152997838-c183a0f3-b246-480d-a381-3352e7f61e92.png)

7. Continue to sign-in via Google (OIDC)

![image](https://user-images.githubusercontent.com/39940531/152997863-8e293023-d794-4e02-ba1a-c20964dcf00b.png)

8. On successful sign-in, a message will be displayed that the cluster is added.

![image](https://user-images.githubusercontent.com/39940531/152997884-c8ae844c-e61f-42f9-ba9b-12bc43e64be0.png)

9. The new cluster “democluster2” is now listed on the UI.

![image](https://user-images.githubusercontent.com/39940531/152998410-dbe457e2-610b-4e2e-8dae-d11f949d823d.png)

10.	Click on the cluster name to continue using the cluster for further operations.
