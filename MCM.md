# Multi Cluster Management
TrilioVault for Kubernetes ships with a user interface that simplifies management of the solution. It simplifies it by enabling simple click-driven workflows to backup, recover, and migrate applications between Kubernetes clusters (or namespaces) that have _TrilioVault for Kubernetes_ installed in them.

On the **Cluster Management** (Home) page, users can add and navigate clusters. TVK supports authentication via KubeConfig files and via Dex, which is an identity service IDP plugin for other identity providers. Below is the step-by-step guide to add clusters using LDAP and OIDC (Google SSO) authentications. Refer to documentation [here](https://docs.trilio.io/kubernetes/management-console/user-interface/ui-authentication) for other authentication methods.

## Adding a New Cluster

1. Log in to the **Management Console** of _Triliovault for Kubernetes_. 
2. The **Cluster Management** (Home) tab is displayed by default, which displays a list of TV-enabled clusters.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/MCM-image-1.png" width="1200"/>
3. Click **New Cluster** to add a new cluster.
4. Provide the requested details, like **Name**, **URL**, and **Distribution Type**. For example:
   1. Name: democluster1
   2. URL: https://default.k8s-tvk.com/login
   3. Distribution Type: GKE
      
      <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/MCM-image-2.png" width="800"/>
5. Authenticate the new cluster using Kubeconfig or LDAP/OIDC authentication. 
   1. For Kubecobnfig, click on this field to browse your local system to select a previously configured Kubeconfig file. Then click **Add**.
   2. To sign-in via LDAP, first make sure that the LDAP option is displayed. If it is, click **Sign-in via LDAP** and then sign in with your LDAP username and password and click **Login**.
      
      <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/MCM-image-3.png" width="400"/>
7. Following successful sign-in, a green banner displays on your screen with the confirmation **Cluster Added**.
8. On the **Cluster Management** (Home) tab, check that the new cluster (_democluster1_) is now added to the **CLUSTERS** list the UI.
9. Click your new cluster to use it for further operations.

## Adding Another Cluster
1. To add another cluster, repeat steps 1-4 from the previous section (Adding a New Cluster).
2. Click **Sign-in via Google** (OIDC).
3. On the login screen displayed, select the Google account that you wish to sign in with. If you are not already logged in to your Google account, you will be prompted to sign in with your Google username and password.
   <img src="https://github.com/sachin-trilio/HowTos/blob/main/media/MCM-image-4.png" width="400"/>
4. Following successful sign-in, a green banner displays on your screen with the confirmation **Cluster Added**.
5. On the **Cluster Management** (Home) tab, check that the new cluster (_democluster1_) is now added to the **CLUSTERS** list the UI.
6. Click your new cluster to use it for further operations.
