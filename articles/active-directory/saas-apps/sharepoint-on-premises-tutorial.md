---
title: 'Tutoriel : Intégration d’Azure Active Directory à SharePoint (local) | Microsoft Docs'
description: Découvrez comment implémenter l’authentification fédérée entre Azure Active Directory et SharePoint local.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: jeedes
ms.openlocfilehash: d168c2c442fba70d021e90daeecce5844adfb274
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112463424"
---
# <a name="tutorial-implement-federated-authentication-between-azure-active-directory-and-sharepoint-on-premises"></a>Tutoriel : Implémenter l’authentification fédérée entre Azure Active Directory et SharePoint local

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous configurez une authentification fédérée entre Azure Active Directory et SharePoint local. L’objectif est de permettre aux utilisateurs de se connecter à Azure Active Directory et d’utiliser leur identité pour accéder aux sites SharePoint locaux.

## <a name="prerequisites"></a>Prérequis

Pour effectuer la configuration, vous avez besoin des ressources suivantes :
* Un locataire Azure Active Directory. Si vous n’en avez pas, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/).
* Une batterie de serveurs SharePoint 2013 ou version ultérieure.

Cet article utilise les valeurs suivantes :
- Nom de l’application d’entreprise (dans Azure AD) : `SharePoint corporate farm`
- Identificateur d’approbation (dans Azure AD) / domaine (dans SharePoint) : `urn:sharepoint:federation`
- URL de connexion (à Azure AD) : `https://login.microsoftonline.com/dc38a67a-f981-4e24-ba16-4443ada44484/wsfed`
- URL du site SharePoint : `https://spsites.contoso.local/`
- URL de réponse du site SharePoint : `https://spsites.contoso.local/_trust/`
- Nom de la configuration d’approbation SharePoint : `AzureADTrust`
- Nom d’utilisateur principal de l’utilisateur de test Azure AD : `AzureUser1@demo1984.onmicrosoft.com`

## <a name="configure-an-enterprise-application-in-azure-active-directory"></a>Configurer une application d’entreprise dans Azure Active Directory

Pour configurer la fédération dans Azure AD, vous devez créer une application d’entreprise dédiée. Sa configuration est simplifiée grâce au modèle préconfiguré `SharePoint on-premises` qui se trouve dans la galerie d’applications.

### <a name="create-the-enterprise-application"></a>Créer une application d’entreprise

1. Connectez-vous au [portail Azure Active Directory](https://aad.portal.azure.com/).
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une application, sélectionnez **Nouvelle application** en haut de la boîte de dialogue.
1. Dans la zone de recherche, entrez le filtre **SharePoint (local)** . Dans le volet résultats, sélectionnez **SharePoint (local)** .
1. Spécifiez un nom pour votre application (dans ce tutoriel, c’est `SharePoint corporate farm`), puis cliquez sur **Créer** pour ajouter l’application.
1. Dans la nouvelle application d’entreprise, sélectionnez **Propriétés** et vérifiez la valeur de l’option **Affectation de l’utilisateur obligatoire**. Pour ce scénario, définissez sa valeur sur **Non**, puis cliquez sur **Enregistrer**.

### <a name="configure-the-enterprise-application"></a>Configurer l’application d’entreprise

Dans cette section, vous configurez l’authentification SAML et vous définissez les revendications qui seront envoyées à SharePoint en cas d’authentification réussie.

1. Dans la vue d’ensemble de l’application d’entreprise `SharePoint corporate farm`, sélectionnez **2. Configurez l’authentification unique** et choisissez **SAML** dans la boîte de dialogue suivante.
 
1. Dans la page **Configurer l’authentification unique avec SAML**, sélectionnez l’icône **Modifier** dans le volet **Configuration SAML de base**.

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    1. Dans la zone **Identificateur**, vérifiez que cette valeur est présente : `urn:sharepoint:federation`.

    1. Dans la zone **URL de réponse**, entrez une URL au format suivant : `https://spsites.contoso.local/_trust/`.

    1. Dans la zone **URL de connexion**, entrez une URL au format suivant : `https://spsites.contoso.local/`.
    
    1. Sélectionnez **Enregistrer**.

1. Dans la section **Attributs et revendications de l’utilisateur**, supprimez les types de revendication suivants, qui ne sont pas utilisables car ils ne seront pas utilisés par SharePoint pour accorder des autorisations :
    - `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`
    - `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`
    - `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

1. Les paramètres devraient maintenant se présenter ainsi :

    ![Paramètres SAML de base](./media/sharepoint-on-premises-tutorial/azure-active-directory-app-saml-ids.png)

1. Copiez les informations dont vous aurez besoin plus tard dans SharePoint :

    - Dans la section **Certificat de signature SAML**, **téléchargez** le **Certificat (en base64)** . Il s’agit de la clé publique du certificat de signature utilisé par Azure AD pour signer le jeton SAML. SharePoint en aura besoin pour vérifier l’intégrité des jetons SAML entrants.

    - Dans la section **Configurer la batterie de serveurs d’entreprise SharePoint**, copiez l’ **URL de connexion** dans un Bloc-notes et remplacez la chaîne de fin **/saml2** par **/wsfed**.
     
    > [!IMPORTANT]
    > Veillez à remplacer **/saml2** par **/wsfed** pour faire en sorte qu’Azure AD émette un jeton SAML 1.1, comme requis par SharePoint.

    - Dans la section **Configurer la batterie de serveurs d’entreprise SharePoint**, copiez l’**URL de déconnexion**.

## <a name="configure-sharepoint-to-trust-azure-active-directory"></a>Configurer SharePoint pour approuver Azure Active Directory

### <a name="create-the-trust-in-sharepoint"></a>Créer la confiance dans SharePoint

Dans cette étape, vous créez un SPTrustedLoginProvider pour stocker la configuration dont SharePoint a besoin pour approuver Azure AD. Pour cela, vous avez besoin des informations d’Azure AD que vous avez copiées ci-dessus. Démarrez SharePoint Management Shell et exécutez le script suivant pour le créer :

```powershell
# Path to the public key of the Azure AD SAML signing certificate (self-signed), downloaded from the Enterprise application in the Azure AD portal
$signingCert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2("C:\AAD app\SharePoint corporate farm.cer")
# Unique realm (corresponds to the "Identifier (Entity ID)" in the Azure AD Enterprise application)
$realm = "urn:sharepoint:federation"
# Login URL copied from the Azure AD enterprise application. Make sure to replace "saml2" with "wsfed" at the end of the URL:
$loginUrl = "https://login.microsoftonline.com/dc38a67a-f981-4e24-ba16-4443ada44484/wsfed"

# Define the claim types used for the authorization
$userIdentifier = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
$role = New-SPClaimTypeMapping "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming

# Let SharePoint trust the Azure AD signing certificate
New-SPTrustedRootAuthority -Name "Azure AD signing certificate" -Certificate $signingCert

# Create a new SPTrustedIdentityTokenIssuer in SharePoint
$trust = New-SPTrustedIdentityTokenIssuer -Name "AzureADTrust" -Description "Azure AD" -Realm $realm -ImportTrustCertificate $signingCert -ClaimsMappings $userIdentifier, $role -SignInUrl $loginUrl -IdentifierClaim $userIdentifier.InputClaimType
```

### <a name="configure-the-sharepoint-web-application"></a>Configurer l’application web SharePoint

Dans cette étape, vous configurez une application web dans SharePoint pour approuver l’application d’entreprise Azure AD créée ci-dessus. Ayez à l’esprit certaines règles importantes :
- L’authentification Windows doit être activée dans la zone par défaut de l’application web SharePoint. C’est nécessaire pour le robot de recherche.
- L’URL SharePoint qui va utiliser l’authentification Azure AD doit être définie avec le protocole HTTPS.

1. Créez ou étendez l’application web. Cet article décrit deux configurations possibles :

    - Si vous créez une application web qui utilise à la fois l’authentification Windows et l’authentification Azure AD dans la zone par défaut :

        1. Démarrez le **shell d’administration SharePoint** et exécutez le script suivant :
            ```powershell
            # This script creates a new web application and sets Windows and Azure AD authentication on the Default zone
            # URL of the SharePoint site federated with Azure AD
            $trustedSharePointSiteUrl = "https://spsites.contoso.local/"
            $applicationPoolManagedAccount = "Contoso\spapppool"

            $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$true
            $sptrust = Get-SPTrustedIdentityTokenIssuer "AzureADTrust"
            $trustedAp = New-SPAuthenticationProvider -TrustedIdentityTokenIssuer $sptrust    
            
            New-SPWebApplication -Name "SharePoint - Azure AD" -Port 443 -SecureSocketsLayer -URL $trustedSharePointSiteUrl -ApplicationPool "SharePoint - Azure AD" -ApplicationPoolAccount (Get-SPManagedAccount $applicationPoolManagedAccount) -AuthenticationProvider $winAp, $trustedAp
            ```
        1. Ouvrez le site **Administration centrale de SharePoint**.
        1. Sous **Paramètres système**, sélectionnez **Configurer des mappages d’accès alternatifs**. La zone **Collection de mappages d’accès alternatif** s’ouvre.
        1. Filtrez l’affichage avec la nouvelle application web et vérifiez que vous voyez ce qui suit :
    
           ![Mappages d’accès de substitution de l’application web](./media/sharepoint-on-premises-tutorial/sp-alternate-access-mappings-new-web-app.png)

    - Si vous étendez une application web existante pour qu’elle utilise l’authentification Azure AD sur une nouvelle zone :

        1. Démarrez SharePoint Management Shell et exécutez le script suivant :

            ```powershell
            # This script extends an existing web application to set Azure AD authentication on a new zone
            # URL of the default zone of the web application
            $webAppDefaultZoneUrl = "http://spsites/"
            # URL of the SharePoint site federated with ADFS
            $trustedSharePointSiteUrl = "https://spsites.contoso.local/"
            $sptrust = Get-SPTrustedIdentityTokenIssuer "AzureADTrust"
            $ap = New-SPAuthenticationProvider -TrustedIdentityTokenIssuer $sptrust
            $wa = Get-SPWebApplication $webAppDefaultZoneUrl
            
            New-SPWebApplicationExtension -Name "SharePoint - Azure AD" -Identity $wa -SecureSocketsLayer -Zone Internet -Url $trustedSharePointSiteUrl -AuthenticationProvider $ap
            ```
        
        1. Ouvrez le site **Administration centrale de SharePoint**.
        1. Sous **Paramètres système**, sélectionnez **Configurer des mappages d’accès alternatifs**. La zone **Collection de mappages d’accès alternatif** s’ouvre.
        1. Filtrez l’affichage avec l’application web qui a été étendue et vérifiez que vous voyez ce qui suit :
    
            ![Mappages d’accès alternatifs de l’application web étendue](./media/sharepoint-on-premises-tutorial/sp-alternate-access-mappings-extended-zone.png)

Une fois l’application web créée, vous pouvez créer une collection de sites racine et ajouter votre compte Windows en tant qu’administrateur de la collection de sites principale.

1. Créer un certificat pour le site SharePoint

    Comme l’URL SharePoint utilise le protocole HTTPS (`https://spsites.contoso.local/`), un certificat doit être défini sur le site Internet Information Services (IIS) correspondant. Effectuez ces étapes pour générer un certificat auto-signé :
    
    > [!IMPORTANT]
    > Les certificats auto-signés sont uniquement destinés à des fins de test. Pour les environnements de production, nous vous recommandons vivement d’utiliser des certificats émis par une autorité de certification à la place.
    
    1. Ouvrez la console Windows PowerShell.
    1. Exécutez le script suivant pour générer un certificat auto-signé et l’ajouter à MON magasin sur l’ordinateur :
    
        ```powershell
        New-SelfSignedCertificate -DnsName "spsites.contoso.local" -CertStoreLocation "cert:\LocalMachine\My"
        ```
    
1. Définir le certificat dans le site IIS
    1. Ouvrez la console du gestionnaire des services Internet Information Services.
    1. Développez le serveur dans l’arborescence, développez **Sites**, sélectionnez le site **SharePoint – Azure AD**, puis **Liaisons**.
    1. Sélectionnez la **liaison https**, puis **Modifier**.
    1. Dans le champ Certificat TLS/SSL, choisissez le certificat à utiliser (par exemple **spsites.contoso.local** créé ci-dessus), puis sélectionnez **OK**.
    
    > [!NOTE]
    > Si vous avez plusieurs serveurs front-ends web, vous devez répéter cette opération sur chacun d’eux.

La configuration de base de l’approbation entre SharePoint et Azure AD est maintenant terminée. Voyons comment se connecter au site SharePoint en tant qu’utilisateur Azure Active Directory.

## <a name="sign-in-as-a-member-user"></a>Se connecter en tant qu’utilisateur membre

Azure Active Directory a [deux types d’utilisateurs](../external-identities/user-properties.md) : les utilisateurs invités et les utilisateurs membres. Commençons par un utilisateur membre, qui est simplement un utilisateur hébergé dans votre organisation.

### <a name="create-a-member-user-in-azure-active-directory"></a>Créer un utilisateur membre dans Azure Active Directory

1. Dans le portail Azure, dans le volet de gauche, sélectionnez **Azure Active Directory**. Dans le volet **Gérer**, sélectionnez **Utilisateurs**.

1. Sélectionnez **Tous les utilisateurs** > **Nouvel utilisateur** dans la partie supérieure de l’écran.

1. Sélectionnez **Créer un utilisateur** puis, dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    1. Dans la zone **Nom**, entrez le nom d’utilisateur. Nous avons utilisé **TestUser**.
  
    1. Dans la zone **Nom d’utilisateur**, entrez `AzureUser1@<yourcompanytenant>.onmicrosoft.com`. Cet exemple montre `AzureUser1@demo1984.onmicrosoft.com` :

       ![Boîte de dialogue Utilisateur](./media/sharepoint-on-premises-tutorial/azure-active-directory-new-user.png)

    1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans la zone **Mot de passe**.

    1. Sélectionnez **Create** (Créer).

    1. Vous pouvez maintenant partager le site avec `AzureUser1@demo1984.onmicrosoft.com` et autoriser cet utilisateur à y accéder.

### <a name="grant-permissions-to-the-azure-active-directory-user-in-sharepoint"></a>Accorder des autorisations à l’utilisateur Azure Active Directory dans SharePoint

Connectez-vous à la collection de sites racine SharePoint en tant que compte Windows (administrateur de la collection de sites), puis cliquez sur **Partager**.  
Dans la boîte de dialogue, vous devez taper la valeur exacte du nom d’utilisateur principal, par exemple `AzureUser1@demo1984.onmicrosoft.com`, et veiller à sélectionner le résultat de la revendication de **nom** (déplacez votre souris sur un résultat pour voir son type de revendication).

> [!IMPORTANT]
> Veillez à taper la valeur exacte de l’utilisateur que vous voulez inviter, puis choisissez le type de revendication approprié dans la liste ; sinon, le partage ne fonctionnera pas.

![Résultats du sélecteur de personnes sans AzureCP](./media/sharepoint-on-premises-tutorial/sp-people-picker-search-no-azurecp.png)

Cette limitation est due au fait que SharePoint ne valide pas l’entrée du sélecteur de personnes, ce qui peut prêter à confusion et entraîner des fautes d’orthographe ou des utilisateurs qui choisissent accidentellement un type de revendication erroné.  
Pour remédier à ce scénario, une solution open source appelée [AzureCP](https://yvand.github.io/AzureCP/) peut être utilisée pour connecter SharePoint 2019/2016/2013 à Azure Active Directory et résoudre l’entrée par rapport à votre locataire Azure Active Directory. Pour plus d’informations, consultez [AzureCP](https://yvand.github.io/AzureCP/).

Voici la même recherche avec AzureCP configuré : SharePoint retourne les utilisateurs réels en fonction de l’entrée :

![Résultats du sélecteur de personnes avec AzureCP](./media/sharepoint-on-premises-tutorial/sp-people-picker-search-with-azurecp.png)

> [!IMPORTANT]
> AzureCP n’est pas un produit Microsoft et n’est pas pris en charge par Support Microsoft. Pour télécharger, installer et configurer le fournisseur de revendications Azure sur la batterie de serveurs SharePoint (local), consultez le site web d’[AzureCP](https://yvand.github.io/AzureCP/). 

L’utilisateur Azure Active Directory `AzureUser1@demo1984.onmicrosoft.com` peut désormais utiliser son identité pour se connecter au site SharePoint `https://spsites.contoso.local/`.

## <a name="grant-permissions-to-a-security-group"></a>Accorder des autorisations à un groupe de sécurité

### <a name="add-the-group-claim-type-to-the-enterprise-application"></a>Ajouter le type de revendication de groupe à l’application d’entreprise

1. Dans la vue d’ensemble de l’application d’entreprise `SharePoint corporate farm`, sélectionnez **2. Configurer l’authentification unique**. 

1. Dans la section **Attributs et revendications de l’utilisateur**, effectuez ces étapes si aucune revendication de groupe n’est présente :

    1. Sélectionnez **Ajouter une revendication de groupe**, sélectionnez **Groupes de sécurité**, et vérifiez que l’**Attribut source** est défini sur **ID de groupe**.
    1. Cochez la case **Personnaliser le nom de la revendication de groupe**, puis cochez **Émettre des revendications de groupes en tant que rôles**, puis cliquez sur **Enregistrer**.
    1. La section **Attributs et revendications de l’utilisateur** doit se présenter comme ceci :

    ![Revendications pour les utilisateurs et les groupes](./media/sharepoint-on-premises-tutorial/azure-active-directory-claims-with-group.png)

### <a name="create-a-security-group-in-azure-active-directory"></a>Créer un groupe de sécurité dans Azure Active Directory

Créons un groupe de sécurité dans Azure Active Directory :

1. Sélectionnez **Azure Active Directory** > **Groupes**.

1. Sélectionnez **Nouveau groupe**.

1. Renseignez le **Type de groupe** (Sécurité), le **Nom du groupe** (par exemple `AzureGroup1`) et le **Type d’appartenance**. Ajoutez l’utilisateur que vous avez créé ci-dessus en tant que membre, puis sélectionnez **Créer** :

    ![Créer un groupe de sécurité Azure AD](./media/sharepoint-on-premises-tutorial/azure-active-directory-new-group.png)
  
### <a name="grant-permissions-to-the-security-group-in-sharepoint"></a>Accorder des autorisations au groupe de sécurité dans SharePoint

Les groupes de sécurité Azure AD sont identifiés avec leur attribut `Id`, qui est un GUID (par exemple `E89EF0A3-46CC-45BF-93A4-E078FCEBFC45`).  
Sans un fournisseur de revendications personnalisé, les utilisateurs doivent taper la valeur exacte (`Id`) du groupe dans le sélecteur de personnes et sélectionner le type de revendication correspondant. Ceci n’est ni convivial ni fiable.  
Pour éviter cela, cet article utilise le fournisseur de revendications tiers [AzureCP](https://yvand.github.io/AzureCP/) pour rechercher le groupe de façon conviviale dans SharePoint :

![Sélecteur de personnes - Rechercher un groupe Azure AD](./media/sharepoint-on-premises-tutorial/sp-people-picker-search-azure-active-directory-group.png)

## <a name="manage-guest-users-access"></a>Gérer l’accès d’utilisateurs invités

Il existe deux types de comptes Invité :

- Comptes Invité B2B : ces utilisateurs sont hébergés dans un locataire Azure Active Directory externe
- Comptes Invité MSA : ces utilisateurs sont hébergés auprès d’un fournisseur d’identité Microsoft (Hotmail, Outlook) ou d’un fournisseur de comptes sociaux (Google ou similaire)

Par défaut, Azure Active Directory affecte à la fois l’« dentificateur d’utilisateur unique » et le « nom » de revendication à l’attribut `user.userprincipalname`.  
Malheureusement, cet attribut est ambigu pour les comptes Invité, comme le montre le tableau ci-dessous :

| Attribut source défini dans Azure AD | Propriété réelle utilisée par Azure AD pour les invités B2B | Propriété réelle utilisée par Azure AD pour les invités MSA | Propriété sur laquelle SharePoint peut s’appuyer pour valider l’identité |
|--|--|--|--|
| `user.userprincipalname` | `mail`, par exemple : `guest@PARTNERTENANT` | `userprincipalname`, par exemple : `guest_outlook.com#EXT#@TENANT.onmicrosoft.com` | ambiguous |
| `user.localuserprincipalname` | `userprincipalname`, par exemple : `guest_PARTNERTENANT#EXT#@TENANT.onmicrosoft.com` | `userprincipalname`, par exemple : `guest_outlook.com#EXT#@TENANT.onmicrosoft.com` | `userprincipalname` |

En conclusion, pour faire en sorte que les comptes Invité soient tous identifiés avec le même attribut, les revendications d’identificateur de l’application d’entreprise doivent être mises à jour de façon à utiliser l’attribut `user.localuserprincipalname` au lieu de `user.userprincipalname`.

### <a name="update-the-application-to-use-a-consistent-attribute-for-all-guest-users"></a>Mettre à jour l’application pour utiliser un attribut cohérent pour tous les utilisateurs invités

1. Dans la vue d’ensemble de l’application d’entreprise `SharePoint corporate farm`, sélectionnez **2. Configurer l’authentification unique**.
 
1. Dans la page **Configurer l’authentification unique avec SAML**, sélectionnez l’icône **Modifier** dans le volet **Attributs et revendications de l’utilisateur**.

1. Dans la section **Attributs et revendications de l’utilisateur**, effectuez ces étapes :

    1. Sélectionnez **Identificateur d’utilisateur unique (ID nom)** , remplacez sa propriété **Attribut source** par **user.localuserprincipalname**, puis cliquez sur **Enregistrer**.
    
    1. Sélectionnez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` , remplacez sa propriété **Attribut source** par **user.localuserprincipalname**, puis cliquez sur **Enregistrer**.
    
    1. La section **Attributs et revendications de l’utilisateur** doit se présenter comme ceci :
    
    ![Attributs et revendications de l’utilisateur pour les invités](./media/sharepoint-on-premises-tutorial/azure-active-directory-claims-guests.png)

### <a name="invite-guest-users-in-sharepoint"></a>Inviter des utilisateurs invités dans SharePoint

> [!NOTE]
> Cette section part du principe que le fournisseur de revendications AzureCP est utilisé.

Dans la section ci-dessus, vous avez mis à jour l’application d’entreprise pour utiliser un attribut cohérent pour tous les comptes Invité.  
À présent, la configuration d’AzureCP doit être mise à jour pour refléter cette modification et utiliser l’attribut `userprincipalname` pour les comptes Invité :

1. Ouvrez le site **Administration centrale de SharePoint**.
1. Sous **Sécurité**, sélectionnez **Configuration globale d’AzureCP**.
1. Dans la section **Propriété de l’identificateur d’utilisateur**, définissez l’**Identificateur d’utilisateur pour les utilisateurs « Invité »** sur **userPrincipalName**.
1. Cliquez sur OK.

![Configuration de comptes Invité AzureCP](./media/sharepoint-on-premises-tutorial/sp-azurecp-attribute-for-guests.png)

Vous pouvez maintenant inviter des utilisateurs invités dans les sites SharePoint.

## <a name="configure-the-federation-for-multiple-web-applications"></a>Configurer la fédération pour plusieurs applications web

La configuration peut s’appliquer à une application web. Toutefois, une configuration supplémentaire est nécessaire si vous prévoyez d’utiliser le même fournisseur d’identité approuvé pour plusieurs applications web. Par exemple, supposons que vous avez une application web distincte `https://otherwebapp.contoso.local/` et que vous voulez maintenant activer l’authentification Azure Active Directory sur celle-ci. Pour cela, configurez SharePoint pour qu’il passe le paramètre SAML WReply et ajoutez les URL dans l’application d’entreprise.

### <a name="configure-sharepoint-to-pass-the-saml-wreply-parameter"></a>Configurer SharePoint pour passer le paramètre SAML WReply

1. Sur le serveur SharePoint, ouvrez SharePoint 201x Management Shell et exécutez les commandes suivantes. Utilisez le même nom pour l’émetteur de jeton d’identité approuvé que celui que vous avez utilisé auparavant.

```powershell
$t = Get-SPTrustedIdentityTokenIssuer "AzureADTrust"
$t.UseWReplyParameter = $true
$t.Update()
```

### <a name="add-the-urls-in-the-enterprise-application"></a>Ajouter les URL dans l’application d’entreprise

1. Sur le portail Azure, sélectionnez **Azure Active Directory** > **Applications d’entreprise**. Sélectionnez le nom de l’application d’entreprise créée précédemment, puis sélectionnez **Authentification unique**.

1. Dans la page **Configurer l’authentification unique avec SAML**, modifiez la section **Configuration SAML de base**.

1. Dans la section **URL de réponse (URL Assertion Consumer Service)** , ajoutez l’URL (par exemple `https://otherwebapp.contoso.local/`) de toutes les applications web supplémentaires qui doivent connecter des utilisateurs avec Azure Active Directory, puis cliquez sur **Enregistrer**.

![Spécifier des applications web supplémentaires](./media/sharepoint-on-premises-tutorial/azure-active-directory-app-reply-urls.png)