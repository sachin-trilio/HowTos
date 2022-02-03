# Authentication
Authentication is the process of verifying identity of a user. 

# Authorization
Authorization is the process of determining if this identified user should have access to a particular resource or not.

# Supported Auth Protocols
## OIDC
OpenID Connect (OIDC) is an authentication protocol based on the OAuth2 protocol (which is used for authorization). OIDC uses the standardized message flows from OAuth2 to provide identity services..

## LDAP/AD
Lightweight Directory Access Protocol (LDAP) is an application protocol for working with various directory services. Directory services, such as Active Directory (AD), store user and account information, and security information like passwords. The service then allows the information to be shared with other devices on the network.

## TVK Auth Support
TVK supports authentication via KubeConfig files and via Dex an IDP plugin for other Identity Providers.

**TVK Callback URL**: http(s)://<ingress-domain>/dex/callback

**OIDC Provider**: Configure your OIDC authentication provider to allow authentication for TVK Web. Create new application on authentication provider portal and use the above callback URL in the oidc provider portal and generate client & secret keys.
1. Google App : https://console.developers.google.com/apis/credentials
2. Github App : https://github.com/settings/applications/new
3. Linkedin App : https://developer.linkedin.com/
4. Okta App : https://developer.okta.com/
5. Gitlab App : https://gitlab.com/-/profile/applications
6. Azure App: https://docs.microsoft.com/en-us/powerapps/maker/portals/configure/configure-openid-provider

**LDAP/AD**: LDAP protocol requires one read-only user that can perform LDAP search to fetch users & groups.

**OpenShift**: By default, Openshift clusters will have Login Via Openshift configured during Triliovault installation, TVK doesn't require any user input for that.

## Steps to configure the Dex login for TVK UI using Google SSO
1. Create credential on AWS - https://console.cloud.google.com/apis/credentials
  
  ![image](https://user-images.githubusercontent.com/39940531/151800767-ac391c39-9ac1-4d36-bf4b-a5240f4e131a.png)
  
2. New credential would include OAuth 2.0 Client IDs, keep the type as Web-Application
  
  ![image](https://user-images.githubusercontent.com/39940531/151800920-98fea70f-225c-427d-a870-3434d3f0aa2a.png)
 
3. Add Authorized redirect URIs as the TVK call back URL: e.g. For the NodePort http://default.k8s-tvk.com:32663/dex/callback and for the LoadBalancer: http://gke-270.k8s-tvk.demo.trilio.io/dex/callback
  
  ![image](https://user-images.githubusercontent.com/39940531/151801000-90cd4588-87db-48fa-aeb8-428a032c11b5.png)

4. Click on “create”
  
5. Copy the client id and client secret displayed 
  
  ![image](https://user-images.githubusercontent.com/39940531/151801100-7cea3532-258d-47ff-afe2-8ce4509b9745.png)

6. Follow https://docs.trilio.io/kubernetes/management-console/user-interface/ui-authentication/oidc-ldap-and-openshift-authentication#configuration
  - Prepare a secret YAML file with the name triliovault-dexwith all the required details of the authentication provider. Refer to the format below and update the required values as needed
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
  - Apply this secret.yaml in the namespace of the k8s cluster where TVK is installed and this will lead to the creation of the TVK dex deployment which will reflect changes on TVK web UI, where you can find another way of login
  ```
  kubectl apply -f triliovault-dex.yaml
  ```
  
  7. Create a cluster role binding using the below command:  Replace the username to your Google email ID and clusterrolebinding name since it has to be unique

  ```
  kubectl create clusterrolebinding admin-binding-1 --clusterrole=cluster-admin --user=<Google ID>
  ```

  8. Check the TVK UI Login page. It should provide “Sign-in via Google” option
  
  ![image](https://user-images.githubusercontent.com/39940531/151807074-d76c0472-2aca-4637-8c4f-285950c496ea.png)
 
9. Continue to login using the google id
  
## Steps to configure the Dex login for TVK UI using Azure OAuth
  
1. Register new application – Go to Home -> App registrations -> Register an application (https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)
  
2. Provide a display name – e.g. TVK-UI-LOGIN
  
  ![image](https://user-images.githubusercontent.com/39940531/151808776-9178e90d-e3a8-478d-9629-a7ddfefd2c12.png)

3. Provide redirect URI “https://default.k8s-tvk.com/dex/callback“ and click Register

  ![image](https://user-images.githubusercontent.com/39940531/151808743-232adddd-0320-47af-a99c-162306f82c32.png)

4. Note down details
  - Application (client) ID - e.g. 9ffef174-16c9-4ac8-b730-295db9cd08d2
  - Token endpoint - https://login.microsoftonline.com/0d4a2397-be27-42b5-9613-6650908518bc/v2.0
  
  ![image](https://user-images.githubusercontent.com/39940531/151808685-5a255e0f-40ff-4751-adc2-037b218a933c.png)

5. Add a client secret and note down client secret value
  
  ![image](https://user-images.githubusercontent.com/39940531/151808620-b6187d6c-93ab-4da3-8a3d-a554af4a072b.png)

6. Configure TVK Management Console access over HTTPS referring to https://docs.trilio.io/kubernetes/management-console/user-interface/accessing-the-ui#access-over-https

7. Configure Azure Authentication following https://docs.trilio.io/kubernetes/management-console/user-interface/ui-authentication/oidc-ldap-and-openshift-authentication#configuration

  - Prepare a secret YAML file with the name triliovault-dexwith all the required details of the authentication provider. Refer to the format below and update the required values as needed
  
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
  
  - Apply this secret.yaml in the namespace of the k8s cluster where TVK is installed and this will lead to the creation of the TVK dex deployment which will reflect changes on TVK web UI, where you can find another way of login
  
  ```
    kubectl apply -f triliovault-dex-azure.yaml
  ```
  
8. Create a cluster role binding using the below command:  Replace the username with MS email ID and clusterrolebinding name since it has to be unique
  
  ```
    kubectl create clusterrolebinding admin-binding-az --clusterrole=cluster-admin --user=<MS Email id>
  ```
  
9. Check the TVK UI Login page. It should provide “Sign-in via Azure” option
    
  ![image](https://user-images.githubusercontent.com/39940531/151808552-e4a04936-17db-4bd0-8fb3-964a8d61b526.png)

10.	Continue to login using the MS Email ID

## Steps to configure the Dex login for TVK UI using LDAP

1. Pre-requisite - LDAP server deployed and configured

2. Retrieve below details from LDAP server
  - LDAP server host IP or FQDN e.g. ldap.tvkdemo.org
  - DNS domain name e.g. tvkdemo.org
  - bindDN: "cn=<username>,dc=tvkdemo,dc=org"
  - bindPW: <password>

3. Configure LDAP Authentication following https://docs.trilio.io/kubernetes/management-console/user-interface/ui-authentication/oidc-ldap-and-openshift-authentication#configuration

  - Prepare a secret YAML file with the name triliovault-dex with all the required details of the authentication provider. Refer to the format below and update the required values as needed
  
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
  
  - Apply this secret.yaml in the namespace of the k8s cluster where TVK is installed and this will lead to the creation of the TVK dex deployment which will reflect changes on TVK web UI, where you can find another way of login
  
  ```
    kubectl apply -f triliovault-dex-ldap.yaml
  ```
  
4. Create a cluster role binding using the below command:  Replace the username with LDAP user ID and clusterrolebinding name since it has to be unique
  
  ```
    kubectl create clusterrolebinding admin-binding-ldap --clusterrole=cluster-admin --user=<LDAP User id>
  ```
  
5. Check the TVK UI Login page. It should provide “Sign-in via LDAP” option
    
  ![image](https://user-images.githubusercontent.com/39940531/152344973-5e072801-1851-47db-bbd4-76ef25c2351b.png)

6. Click on “Sign-in via LDAP” and it will prompt for Username and Password
  
  ![image](https://user-images.githubusercontent.com/39940531/152345100-6693f6e2-0663-4ac3-9080-745a54e07e6b.png)

7.	Continue to login using LDAP username and password
