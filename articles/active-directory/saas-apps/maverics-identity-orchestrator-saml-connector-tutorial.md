---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Maverics Identity Orchestrator SAML Connector | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Maverics Identity Orchestrator SAML Connector.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: jeedes
ms.openlocfilehash: ec5368427f50f548be965bb883683c859759bbf3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88518926"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-maverics-identity-orchestrator-saml-connector"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Maverics Identity Orchestrator SAML Connector

## <a name="introduction"></a>Introduction

Strata offre un moyen simple d’intégrer des applications locales à Azure AD pour l’authentification et le contrôle d’accès.

Ce guide explique comment configurer Maverics Identity Orchestrator&trade; pour :
* Migrer de manière incrémentielle les utilisateurs d’un système d’identité local vers Azure AD lors de la connexion à une application locale héritée
* Router les demandes de connexion à partir d’un produit de gestion de l’accès web hérité, tel que CA SiteMinder ou Oracle Access Manager, vers Azure AD
* Authentifier l’utilisateur auprès des applications locales qui sont protégées à l’aide d’en-têtes HTTP ou de cookies de session propriétaires après l’authentification de l’utilisateur sur Azure AD

Strata fournit un logiciel qui se déploie localement ou dans le cloud à des fins de découverte, de connexion et d’orchestration des fournisseurs d’identité afin de créer une gestion distribuée des identités pour les entreprises hybrides et multiclouds.

Ce tutoriel montre comment migrer une application web locale protégée par un produit de gestion de l’accès web hérité (SiteMinder) afin d’utiliser Azure AD pour l’authentification et le contrôle d’accès.
1. Installer Maverics Identity Orchestrator&trade;
2. Inscrire votre application d’entreprise auprès d’Azure AD et la configurer afin qu’elle utilise Maverics Azure AD SAML Zero Code Connector&trade; pour l’authentification unique SAML
3. Intégrer Maverics à SiteMinder et au magasin d’utilisateurs LDAP
4. Configurer Azure Key Vault et configurez Maverics pour l’utiliser en tant que fournisseur de gestion des secrets
5. Illustrer la migration d’utilisateurs et l’abstraction de session à l’aide de Maverics pour fournir l’accès à une application web Java locale

Pour obtenir des instructions d’installation et de configuration supplémentaires, consultez https://strata.io/docs.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
- Abonnement Maverics Identity Orchestrator SAML Connector pour lequel l’authentification unique est activée. Pour obtenir le logiciel Maverics, contactez sales@strata.io.

## <a name="install-maverics-identity-orchestratortrade"></a>Installer Maverics Identity Orchestrator&trade;

Pour vous familiariser avec l’installation de Maverics Identity Orchestrator, reportez-vous aux instructions d’installation à l’adresse https://strata.io/docs.

## <a name="system-requirements"></a>Configuration système requise
### <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge
* RHEL 7+
* CentOS 7+

### <a name="dependencies"></a>Les dépendances
* systemd

## <a name="installation"></a>Installation

1. Obtenez le package RPM Maverics le plus récent. Copiez le package sur le système sur lequel vous souhaitez installer le logiciel Maverics.

2. Installez le package Maverics, en substituant le nom de votre fichier à `maverics.rpm`.

    `sudo rpm -Uvf maverics.rpm`

3. Une fois Maverics installé, il s’exécute en tant que service sous `systemd`. Pour vérifier que le service est en cours d’exécution, exécutez la commande suivante.

    `sudo systemctl status maverics`

Par défaut, Maverics est installé dans le répertoire `/usr/local/bin`.

Une fois Maverics installé, le fichier `maverics.yaml` par défaut est créé dans le répertoire `/etc/maverics`. Avant que vous ne modifiiez votre configuration pour inclure des `workflows` et des `connectors`, votre fichier de configuration se présente comme suit :

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```
## <a name="config-options"></a>Options de configuration
### <a name="version"></a>Version
Le champ `version` déclare la version du fichier de configuration qui est utilisée. Si ce champ n’est pas spécifié, la version de configuration la plus récente est utilisée.

```yaml
version: 0.1
```
### <a name="listen-address"></a>Adresse d'écoute
`listenAddress` déclare l’adresse qu’Orchestrator doit écouter. Si la section hôte de l’adresse est vide, Orchestrator doit écouter toutes les adresses IP de monodiffusion et anycast disponibles du système local. Si la section du port de l’adresse est vide, un numéro de port est automatiquement choisi.

```yaml
listenAddress: ":453"
```
### <a name="tls"></a>TLS

Le champ `tls` déclare un mappage des objets TLS (Transport Layer Security). Les objets TLS peuvent être utilisés par les connecteurs ainsi que par le serveur Orchestrator. Pour toutes les options TLS disponibles, consultez la documentation du package de `transport`.

Microsoft Azure nécessite une communication sur TLS lors de l’utilisation de l’authentification unique SAML ; vous trouverez plus d’informations [ici](https://letsencrypt.org/getting-started/) pour générer vos certificats.

La clé `maverics` est réservée au serveur Orchestrator. Toutes les autres clés sont disponibles et peuvent être utilisées pour injecter un objet TLS dans un connecteur donné.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```  
### <a name="include-files"></a>Fichiers include

Les `connectors` et `workflows` peuvent être définis dans leurs propres fichiers de configuration et référencés par `maverics.yaml` à l’aide d’`includeFiles`, comme l’illustre l’exemple suivant.
```yaml
includeFiles:
  - workflow/sessionAbstraction.yaml
  - connector/AzureAD-saml.yaml
  - connector/siteminder.yaml
  ```

Ce tutoriel utilise un seul fichier de configuration `maverics.yaml`.

## <a name="using-azure-key-vault-as-your-secrets-provider"></a>Utilisation d’Azure Key Vault comme fournisseur de secrets

### <a name="secret-management"></a>Gestion des secrets

Maverics peut s’intégrer à différentes solutions de gestion des secrets afin de charger les secrets. Les intégrations actuelles incluent un fichier, Hashicorp Vault et Azure Key Vault. Si aucune solution de gestion des secrets n’est spécifiée, Maverics charge par défaut les secrets en texte brut à partir de `maverics.yaml`.
Pour déclarer une valeur en tant que secret dans un fichier de configuration `maverics.yaml`, placez le secret entre crochets angulaires :

  ```yaml
  connectors:
  - name: AzureAD
    type: AzureAD
    apiToken: <AzureADAPIToken>
    oauthClientID: <AzureADOAuthClientID>
    oauthClientSecret: <AzureADOAuthClientSecret>
  ```

### <a name="file"></a>Fichier

Pour charger des secrets à partir d’un fichier, ajoutez la variable d’environnement `MAVERICS_SECRET_PROVIDER` dans le fichier `/etc/maverics/maverics.env`, avec :

`MAVERICS_SECRET_PROVIDER=secretfile:///<PATH TO SECRETS FILE>`

Ensuite, redémarrez le service maverics : `sudo systemctl restart maverics`

Le contenu du fichier `secrets.yaml` peut être rempli avec un nombre quelconque de `secrets`.
```yaml
secrets:
  AzureADAPIToken: aReallyGoodToken
  AzureADOAuthClientID: aReallyUniqueID
  AzureADOAuthClientSecret: aReallyGoodSecret
```
### <a name="azure-key-vault"></a>Azure Key Vault

Les étapes suivantes montrent comment configurer un coffre de clé Azure, à l’aide du [portail Azure](https://portal.azure.com) ou de l’[interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) :

1. [Connectez-vous](https://portal.azure.com) à l’aide du portail Azure ou de la commande CLI suivante :
    ```shell
    az login
    ```

2. [Créez un coffre](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault) à l’aide du portail ou de la commande CLI suivante :
    ```shell
    az keyvault create --name "[VAULT_NAME]" --resource-group "[RESOURCE_GROUP]" --location "[REGION]"
    ```

3. [Ajoutez les secrets au coffre de clés](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault) à l’aide du portail ou d’une commande CLI :
    ```shell
    az keyvault secret set --vault-name "[VAULT_NAME]" --name "[SECRET_NAME]" --value "[SECRET_VALUE]"
    ```

4. [Inscrivez une application auprès d’Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application) à l’aide du portail ou d’une commande CLI :
    ```shell
    az ad sp create-for-rbac -n "MavericsKeyVault" --skip-assignment > azure-credentials.json
    ```

5. [Autorisez une application à utiliser un secret](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault) à l’aide du portail ou d’une commande CLI :
    ```shell
    az keyvault set-policy --name "[VAULT_NAME]" --spn [APPID] --secret-permissions list get
    #APPID can be found in the azure-credentials.json
    generated in the previous step
    ```

Pour charger des secrets à partir d’Azure Key Vault, définissez la variable d’environnement `MAVERICS_SECRET_PROVIDER` dans le fichier `/etc/maverics/maverics.env`, avec les informations d’identification se trouvant dans le fichier azure-credentials.json, en utilisant le modèle suivant : `MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

Ensuite, redémarrez le service maverics : `sudo systemctl restart maverics`

## <a name="configure-your-application-in-azure-ad-for-saml-based-sso"></a>Configurer votre application dans Azure AD pour l’authentification unique SAML

1. Dans votre locataire Azure Active Directory, accédez à `Enterprise applications`, recherchez `Maverics Identity Orchestrator SAML Connector` et sélectionnez-le.

2. Dans la page « Maverics Identity Orchestrator SAML Connector » | Propriétés, définissez `User assignment required?` sur Non afin que l’application fonctionne pour les utilisateurs récemment migrés.

3. Dans la page « Maverics Identity Orchestrator SAML Connector » | Vue d’ensemble, sélectionnez `Setup single sign-on`, puis `SAML`.

4. Dans la page « Maverics Identity Orchestrator SAML Connector » | Authentification basée sur SAML, modifiez la configuration SAML de base.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

5. Définissez l’`Entity ID` tapant une URL au format suivant : `https://<SUBDOMAIN>.maverics.org`. L’`Entity ID` doit être unique dans les applications du locataire. Enregistrez la valeur entrée ici pour l’inclure dans la configuration de Maverics.

6. Définissez l’URL de réponse au format suivant : `https://<AZURECOMPANY.COM>/<MY_APP>/`. 

7. Définissez l’URL de connexion au format `https://<AZURE-COMPANY.COM>/<MY_APP>/<LOGIN PAGE>`, puis cliquez sur Enregistrer.

8. Accédez à la section Certificat de signature SAML, cliquez sur le bouton Copier pour copier l’URL des métadonnées de fédération d’application, puis enregistrez-la sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/copy-metadataurl.png)

## <a name="maverics-identity-orchestrator-azure-ad-saml-connector-configuration"></a>Configuration de Maverics Identity Orchestrator Azure AD SAML Connector

Maverics Identity Orchestrator Azure AD Connector prend en charge : 
- OpenID Connect
- Connexion SAML 

1. Pour activer l’authentification unique SAML, définissez `authType: saml`.

1. Créez la valeur pour `samlMetadataURL` : `samlMetadataURL:https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`

1. À présent, définissez l’URL vers laquelle doit être redirigé l’utilisateur dans votre application une fois qu’il s’est connecté avec ses informations d’identification Azure.
`samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>`

1. Copiez la valeur de l’ID d’entité configuré plus haut : `samlEntityID: https://<SUBDOMAIN>.maverics.org`

1. Copiez la valeur de l’URL de réponse qu’Azure AD doit utiliser pour envoyer la réponse SAML en mode POST.
`samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>`

1. Générez une clé de signature JWT, utilisée pour protéger les informations de session de Maverics Identity Orchestrator&trade;, à l’aide de l’[outil OpenSSL](https://www.openssl.org/source/) :

    ```shell 
    openssl rand 64 | base64
    ```
1. Copiez la réponse dans la propriété de configuration `jwtSigningKey` : `jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==`

## <a name="attributes-and-attribute-mapping"></a>Attributs et mappage d’attributs
Le mappage d’attributs sert à définir le mappage d’attributs utilisateur à partir d’un annuaire d’utilisateurs local source dans Azure AD quand des utilisateurs sont provisionnés.

Les attributs déterminent les données utilisateur qui peuvent être retournées à une application dans une revendication, transmises dans des cookies de session ou transmises à l’application dans des variables d’en-tête http.

## <a name="configure-maverics-identity-orchestrator-azure-ad-saml-connector-yaml"></a>Configurer les informations yaml de Maverics Identity Orchestrator Azure AD SAML Connector

La configuration de Maverics Identity Orchestrator Azure AD Connector se présente comme suit :
```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: <SIGNING KEY>
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

## <a name="migrate-users-to-azure-ad"></a>Migrer des utilisateurs vers Azure AD

Suivez cette configuration pour migrer de façon incrémentielle des utilisateurs à partir d’un produit de gestion de l’accès web, tel que CA SiteMinder, Oracle Access Manager, d’un annuaire LDAP ou d’une base de données SQL.

## <a name="configure-your-application-permissions-in-azure-ad-to-create-users"></a>Configurer vos autorisations d’application dans Azure AD pour créer des utilisateurs

1. Dans votre locataire Azure Active Directory, accédez à `App registrations` et sélectionnez l’application « Maverics Identity Orchestrator SAML Connector ».

2. Dans la page « Maverics Identity Orchestrator SAML Connector » | Certificats et secrets, sélectionnez `New client secret` puis sélectionnez l’option expiration. Cliquez sur le bouton Copier pour copier le secret et enregistrez-le sur votre ordinateur.

3. Dans la page « Maverics Identity Orchestrator SAML Connector » | Autorisations d’API, sélectionnez `Add permission`, puis dans Demander des autorisations d’API, sélectionnez `Microsoft Graph`, puis `Application permissions`. Dans l’écran suivant, sélectionnez `User.ReadWrite.All` puis sélectionnez `Add permissions`. De retour dans Autorisations d’API, sélectionnez `Grant admin consent`.


## <a name="configure-the-maverics-identity-orchestrator-saml-connector-yaml-for-user-migration"></a>Configurer les informations Maverics Identity Orchestrator SAML Connector pour la migration d’utilisateurs

Pour activer le workflow de la migration d’utilisateurs, ajoutez ces propriétés au fichier de configuration :
1. Définissez l’URL d’Azure Graph : `graphURL: https://graph.microsoft.com`
1. Définissez l’URL du jeton OAuth, au format suivant : `oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`
1. Définissez le secret client généré plus haut : `oauthClientSecret: <CLIENT SECRET>`


La configuration finale de Maverics Identity Orchestrator Azure AD Connector se présente comme suit :
```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  graphURL: https://graph.microsoft.com
  oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/oauth2/v2.0/token
  oauthClientID: <APP ID>
  oauthClientSecret: <NEW CLIENT SECRET>
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

## <a name="configure-the-maverics-zero-code-connectortrade-for-siteminder"></a>Configurer Maverics Zero Code Connector&trade; pour SiteMinder

Le connecteur SiteMinder est utilisé pour migrer des utilisateurs vers Azure AD et pour les connecter aux applications locales héritées protégées par SiteMinder à l’aide des identités et des informations d’identification Azure AD créées.

Pour ce tutoriel, SiteMinder a été configuré pour protéger l’application héritée avec l’authentification basée sur les formulaires et à l’aide du cookie `SMSESSION`. Pour une intégration à une application qui consomme des informations d’authentification et de session via des en-têtes HTTP, vous devez ajouter la configuration d’émulation d’en-tête au connecteur.

Cet exemple mappe l’attribut `username` à l’en-tête http `SM_USER` :
```yaml
  headers:
    SM_USER: username
```

Définissez `proxyPass` sur la destination vers laquelle les demandes sont proxysées. En général, il s’agit de l’hôte de l’application protégée.

`loginPage` doit correspondre à l’URL du formulaire de connexion utilisé par SiteMinder lors de la redirection des utilisateurs à des fins d’authentification.

```yaml
connectors:
- name: siteminder-login-form
  type: siteminder
  loginType: form
  loginPage: /siteminderagent/forms/login.fcc
  proxyPass: http://host.company.com
```

## <a name="configure-the-maverics-zero-code-connectortrade-for-ldap"></a>Configurer Maverics Zero Code Connector&trade; pour LDAP

Quand les applications sont protégées par un produit WAM tel que SiteMinder, les identités et les attributs des utilisateurs sont généralement stockés dans un annuaire LDAP.

Cette configuration de connecteur montre comment se connecter à l’annuaire LDAP configuré en tant que magasin d’utilisateurs pour SiteMinder afin que les informations de profil utilisateur appropriées puissent être collectées pendant le workflow de migration et qu’un utilisateur correspondant puisse être créé dans Azure AD.

* `baseDN` spécifie à quel endroit effectuer une recherche LDAP dans l’annuaire.

* `url` désigne l’adresse et le port du serveur LDAP auquel se connecter.

* `serviceAccountUsername` est le nom d’utilisateur utilisé pour se connecter au serveur LDAP, généralement exprimé sous la forme d’un nom unique de liaison, par exemple `CN=Directory Manager`.

* `serviceAccountPassword` est le mot de passe utilisé pour se connecter au serveur LDAP. Cette valeur est stockée dans l’instance Azure Key Vault déjà configurée.  

* `userAttributes` définit la liste d’attributs liés à l’utilisateur à interroger. Ces attributs sont ensuite mappés à des attributs Azure AD correspondants.

```yaml
- name: company-ldap
  type: ldap
  url: "ldap://ldap.company.com:389"
  baseDN: ou=People,o=company,c=US
  serviceAccountUsername: uid=admin,ou=Admins,o=company,c=US
  serviceAccountPassword: <vaulted-password>
  userAttributes:
    - uid
    - cn
    - givenName
    - sn
    - mail
    - mobile
```

## <a name="configure-the-migration-workflow"></a>Configurer le workflow de migration

La configuration du workflow de migration détermine la manière dont Maverics migre les utilisateurs de SiteMinder/LDAP vers Azure AD.

Ce workflow :
- Utilise le connecteur SiteMinder pour proxyser la connexion SiteMinder. Les informations d’identification de l’utilisateur sont validées par le biais de l’authentification SiteMinder, puis transmises aux étapes suivantes du workflow.
- Récupère les attributs de profil utilisateur du magasin d’utilisateurs SiteMinder.
- Envoie une demande à l’API Microsoft Graph pour créer l’utilisateur dans votre locataire Azure AD.

Étapes :
1. Donnez un nom au workflow, par exemple « SiteMinder to Azure AD Migration » (Migration depuis SiteMinder vers Azure AD).
2. Spécifiez `endpoint`, chemin HTTP sur lequel le workflow est exposé, qui déclenche les `actions` de ce workflow en réponse aux demandes. `endpoint` correspond généralement à l’application proxysée, par exemple `/my_app`. La valeur doit inclure des barres obliques de début et de fin.
3. Ajoutez les `actions` appropriées au workflow.
    - Définissez la méthode `login` pour le connecteur SiteMinder. La valeur connector doit correspondre à la valeur name dans la configuration du connecteur.
     - Définissez la méthode `getprofile` pour le connecteur LDAP.
     - Définissez le `createuser` pour le connecteur AzureAD.

    ```yaml
      workflows:
      - name: SiteMinder to Azure AD Migration
        endpoint: /my_app/
        actions:
        - connector: siteminder-login-form
          method: login
        - connector: company-ldap
          method: getprofile
        - connector: AzureAD
          method: createuser
    ```
### <a name="verify-the-migration-workflow"></a>Vérifier le workflow de migration

1. Si le service Maverics n’est pas déjà en cours d’exécution, démarrez-le en exécutant la commande suivante : `sudo systemctl start maverics`

2. Accédez à l’URL de connexion proxysée : `http://host.company.com/my_app`.
3. Fournissez les informations d’identification utilisateur permettant de se connecter à l’application alors que celle-ci est protégée par SiteMinder.
4. Accédez à Accueil > Utilisateurs | Tous les utilisateurs pour vérifier que votre utilisateur est créé dans le locataire Azure AD.  

## <a name="configure-the-session-abstraction-workflow"></a>Configurer le workflow d’abstraction de session

Le workflow d’abstraction de session déplace l’authentification et le contrôle d’accès de l’application web locale héritée vers Azure AD.

Le connecteur Azure utilise la méthode `login` pour rediriger l’utilisateur vers l’URL de connexion, en supposant qu’il n’existe aucune session.

Une fois l’authentification terminée, le jeton de session créé est passé à Maverics, et la méthode `emulate` du connecteur SiteMinder est utilisée pour émuler la session basée sur les cookies et/ou la session basée sur l’en-tête, puis décorer la demande avec les attributs supplémentaires requis par l’application.

1. Donnez un nom au workflow, par exemple « SiteMinder Session Abstraction » (Abstraction de session SiteMinder).
2. Spécifiez `endpoint`, qui correspond à l’application proxysée. La valeur doit inclure des barres obliques de début et de fin, par exemple `/my_app/`.
3. Ajoutez les `actions` appropriées au workflow.
    - Définissez la méthode `login` pour le connecteur Azure. La valeur `connector` doit correspondre à la valeur `name` dans la configuration du connecteur.
    - Définissez la méthode `emulate` pour le connecteur SiteMinder.

     ```yaml
      - name: SiteMinder Session Abstraction
        endpoint: /my_app/
        actions:
      - connector: azure
        method: login
      - connector: siteminder-login-form
        method: emulate
     ```
### <a name="verify-the-session-abstraction-workflow"></a>Vérifier le workflow de l’abstraction de session

1. Accédez à l’URL de l’application proxysée : `https://<AZURECOMPANY.COM>/<MY_APP>`. L’utilisateur est redirigé vers la page de connexion proxysée.
2. Entrez les informations d’identification de l’utilisateur Azure AD.
3. L’utilisateur doit être redirigé vers l’application comme s’il était authentifié directement par SiteMinder.
