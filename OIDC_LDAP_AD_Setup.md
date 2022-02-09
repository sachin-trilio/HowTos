# Authentication
Authentication is the process of verifying the identity of a user. 

# Authorization
Authorization is the process of determining if an identified user has access to a particular resource or not.

# Supported Auth Protocols
## OIDC
OpenID Connect (OIDC) is an authentication protocol based on the OAuth2 protocol (which is used for authorization). OIDC uses the standardized message flows from OAuth2 to provide identity services. It allows clients to confirm an end user's identity using authentication by an authorization server.

## LDAP/AD
Lightweight Directory Access Protocol (LDAP) is an open and cross-platform application protocol for directory services authentication. Directory services, such as Active Directory (AD), store user information, account information, and security information like passwords. The service then allows the information to be shared with other devices on the network.

## TVK Auth Support
TVK supports authentication via KubeConfig files and via Dex, which is an identity service IDP plugin that uses OpenID Connect to drive authentication for multiple applications, by deferring authentication to other identity providers like LDAP.

**TVK Callback URL**: http(s)://<ingress-domain>/dex/callback

**OIDC Provider**: Configure your OIDC authentication provider to allow authentication for TVK Web. Create a new application on the authentication provider portal and then use the above callback URL in the OIDC provider portal. Then generate client & secret keys.
1. Google App : https://console.developers.google.com/apis/credentials
2. Github App : https://github.com/settings/applications/new
3. Linkedin App : https://developer.linkedin.com/
4. Okta App : https://developer.okta.com/
5. Gitlab App : https://gitlab.com/-/profile/applications
6. Azure App: https://docs.microsoft.com/en-us/powerapps/maker/portals/configure/configure-openid-provider

**LDAP/AD**: LDAP protocol requires one read-only user that can perform LDAP searches to fetch users & groups.

**OpenShift**: By default, Openshift clusters have Login Via Openshift configured during Triliovault installation. TVK does not require any user input for that.

## Steps to configure the Dex login for TVK UI using Google SSO
1. Create the credential on AWS - https://console.cloud.google.com/apis/credentials
  
  ![image](https://user-images.githubusercontent.com/39940531/151800767-ac391c39-9ac1-4d36-bf4b-a5240f4e131a.png)
  
2. The new credential includes OAuth 2.0 Client IDs. Keep the type as **Web-Application**.
  
  ![image](https://user-images.githubusercontent.com/39940531/151800920-98fea70f-225c-427d-a870-3434d3f0aa2a.png)
 
3. Add authorized redirect URIs as the TVK call back URL. For example, for the NodePort use http://default.k8s-tvk.com:32663/dex/callback and for the LoadBalancer use: http://gke-270.k8s-tvk.demo.trilio.io/dex/callback
  
  ![image](https://user-images.githubusercontent.com/39940531/151801000-90cd4588-87db-48fa-aeb8-428a032c11b5.png)

4. Click **Create**.
  
5. Copy the **Client ID** and **Client Secret** displayed. 
  
  ![image](https://user-images.githubusercontent.com/39940531/151801100-7cea3532-258d-47ff-afe2-8ce4509b9745.png)

6. Configure authentication by performing the steps listed on this page:  https://docs.trilio.io/kubernetes/management-console/user-interface/ui-authentication/oidc-ldap-and-openshift-authentication#configuration
  - Prepare a secret YAML file with the name triliovault-dexwith all the required details of the authentication provider. Refer to the format below and update the required values as needed:
  ```
  apiVersion: v1
  kind: Secret
  metadata:
    name: triliovault-dex
    labels:
      triliovault.trilio.io/secret: triliovault-dex
  type: Opaque
  stringData:
    TVK_GOOGLE_AUTH: |-
      enabled: true
      type: google
      id: google
      name: Google
      config:
        clientID: 555293725331-9sjcpftlc2s2rfrsp4jps4r5d7fav968.apps.googleusercontent.com
        clientSecret: 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
        redirectURI: http://default.k8s-tvk.com:31607/dex/callback
  ```
  - Apply this secret.yaml in the namespace of the k8s cluster where TVK is installed. This leads to the creation of the TVK dex deployment, which will reflect changes in the TVK web UI, where you will now find another login method.
  ```
  kubectl apply -f triliovault-dex.yaml
  ```
  
  7. Create a cluster role binding using the below command, making sure to replace the _user_ with your Google email ID and the _clusterrolebinding_ name, since it must be unique:

  ```
  kubectl create clusterrolebinding admin-binding-1 --clusterrole=cluster-admin --user=<Google ID>
  ```

  8. Check the TVK UI Login page and ensure that the **Sign-in via Google** option is displayed.
  
  ![image](https://user-images.githubusercontent.com/39940531/151807074-d76c0472-2aca-4637-8c4f-285950c496ea.png)
 
9. Log in using your Google ID.
  
## Steps to configure the Dex login for TVK UI using Azure OAuth
  
1. Register the new application, by first navigating to **Go to Home** -> **App registrations** -> **Register an application**. (https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)
  
2. Provide a display name, e.g. _TVK-UI-LOGIN_
  
  ![image](https://user-images.githubusercontent.com/39940531/151808776-9178e90d-e3a8-478d-9629-a7ddfefd2c12.png)

3. Provide the redirect URI “https://default.k8s-tvk.com/dex/callback“ and click **Register**.

  ![image](https://user-images.githubusercontent.com/39940531/151808743-232adddd-0320-47af-a99c-162306f82c32.png)

4. Note down the following two details:
  - Application (client) ID, e.g. 9ffef174-16c9-4ac8-b730-295db9cd08d2
  - Token endpoint - https://login.microsoftonline.com/0d4a2397-be27-42b5-9613-6650908518bc/v2.0
  
  ![image](https://user-images.githubusercontent.com/39940531/151808685-5a255e0f-40ff-4751-adc2-037b218a933c.png)

5. Add a client secret and note down the client secret value.
  
  ![image](https://user-images.githubusercontent.com/39940531/151808620-b6187d6c-93ab-4da3-8a3d-a554af4a072b.png)

6. Configure the TVK Management Console access over HTTPS, referring to the following documentation guide: https://docs.trilio.io/kubernetes/management-console/user-interface/accessing-the-ui#access-over-https

7. Configure Azure Authentication, referring to the following documentation guide: https://docs.trilio.io/kubernetes/management-console/user-interface/ui-authentication/oidc-ldap-and-openshift-authentication#configuration

  - Prepare a secret YAML file named _triliovault-dex_ that refelcts all the required authentication provider details. Refer to the format below and update the required values as needed:
  
  ```
    apiVersion: v1
    kind: Secret
    metadata:
      name: triliovault-dex
      labels:
        triliovault.trilio.io/secret: triliovault-dex
    type: Opaque
    stringData:
      TVK_OIDC_AUTH: |-
        enabled: true
        type: oidc
        id: azure
        name: azure
        config:
          insecureSkipEmailVerified: true
          issuer: https://login.microsoftonline.com/0d4a2397-be27-42b5-9613-6650908518bc/v2.0
          clientID: 9ffef174-16c9-4ac8-b730-295db9cd08d2
          clientSecret: 1Pm7Q~5UVKLB4U3MT8ZZTjBvnKOVlHufgo0cr
          redirectURI: https://default.k8s-tvk.com/dex/callback
  ```
  
  - Apply this secret.yaml in the namespace of the k8s cluster where TVK is installed. This leads to the creation of the TVK dex deployment, which reflects changes in the TVK web UI, where you will now find another login method.
  
  ```
    kubectl apply -f triliovault-dex-azure.yaml
  ```
  
8. Create a cluster role binding using the below command, making sure to replace the _user_ with MS email ID and the _clusterrolebinding_ name, since it must be unique:
  
  ```
    kubectl create clusterrolebinding admin-binding-az --clusterrole=cluster-admin --user=<MS Email id>
  ```
  
9. Check the TVK UI Login page and ensure that the **Sign-in via Azure** option is displayed.
    
  ![image](https://user-images.githubusercontent.com/39940531/151808552-e4a04936-17db-4bd0-8fb3-964a8d61b526.png)

10.	Continue to log in using the MS Email ID.

## Steps to configure the Dex login for the TVK UI using LDAP

1. Pre-requisite - The LDAP server must be deployed and configured.

2. Retrieve the following details from the LDAP server:
  - LDAP server host IP or FQDN e.g. ldap.tvkdemo.org
  - DNS domain name e.g. tvkdemo.org
  - bindDN: "cn=username,dc=tvkdemo,dc=org"
  - bindPW: password

3. Configure the LDAP Authentication, referring to the following documentation guide: https://docs.trilio.io/kubernetes/management-console/user-interface/ui-authentication/oidc-ldap-and-openshift-authentication#configuration

  - Prepare a secret YAML file named _triliovault-dex_ that refelcts all the required authentication provider details. Refer to the format below and update the required values as needed:
  
  ```
  apiVersion: v1
  kind: Secret
  metadata:
    name: triliovault-dex
    labels:
      triliovault.trilio.io/secret: triliovault-dex
  type: Opaque
  stringData:
    TVK_LDAP_AUTH: |-
      enabled: true
      type: ldap
      id: ldap
      name: "LDAP"
      config:
        host: <HOST IP or FQDN>
        startTLS: false
        insecureNoSSL: true
        insecureSkipVerify: true
        bindDN: "cn=<username>,dc=tvkdemo,dc=org"
        bindPW: <password>
        usernamePrompt: "Username"
        userSearch:
          baseDN: "dc=tvkdemo,dc=org"
          filter: ""
          username: cn
          idAttr: cn
          emailAttr: cn
          nameAttr: cn
        groupSearch:
          baseDN: "dc=tvkdemo,dc=org"
          filter: ""
          userAttr: cn
  ```
  
  - Apply this secret.yaml in the namespace of the k8s cluster where TVK is installed. This leads to the creation of the TVK dex deployment, which reflects changes in the TVK web UI, where you will now find another login method.
  
  ```
    kubectl apply -f triliovault-dex-ldap.yaml
  ```
  
4. Create a cluster role binding using the below command, making sure to replace the _user_ with LDAP user ID and the _clusterrolebinding_ name, since it must be unique:
  
  ```
    kubectl create clusterrolebinding admin-binding-ldap --clusterrole=cluster-admin --user=<LDAP User id>
  ```
  
5. Check the TVK UI Login page and ensure that the **Sign-in via LDAP** option is displayed.
    
  ![image](https://user-images.githubusercontent.com/39940531/152344973-5e072801-1851-47db-bbd4-76ef25c2351b.png)

6. Click **Sign-in via LDAP** and it will prompt for your username and password.
  
  ![image](https://user-images.githubusercontent.com/39940531/152345100-6693f6e2-0663-4ac3-9080-745a54e07e6b.png)

7.	Login using your LDAP username and password.
