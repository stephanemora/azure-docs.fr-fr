---
title: Ajouter Google comme fournisseur d’identité pour B2B – Azure AD
description: Fédérer avec Google pour permettre à des utilisateurs invités de se connecter à vos applications Azure AD avec leur propre compte Gmail
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72c18e48c27942c7bea47931ec79a31af941064e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126664"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Ajouter Google comme fournisseur d’identité pour les utilisateurs invités B2B

En configurant la fédération avec Google, vous pouvez autoriser les utilisateurs invités à se connecter à vos applications et ressources partagées avec leur propre compte Gmail, sans avoir besoin de créer un compte Microsoft (MSA). 

> [!NOTE]
> La fédération Google est conçue spécialement pour les utilisateurs Gmail. Pour fédérer avec les domaines G Suite, utilisez la [fonctionnalité de fédération directe](direct-federation.md).

## <a name="what-is-the-experience-for-the-google-user"></a>Quelle est l’expérience de l’utilisateur Google ?
Lorsque vous envoyez une invitation à un utilisateur de Google Gmail, l’utilisateur invité doit accéder à vos applications ou ressources partagées à l’aide d’un lien qui inclut le contexte locataire. Son expérience varie selon qu’il est ou non déjà connecté à Google :
  - Si l’utilisateur invité n’est pas connecté à Google, il est invité à se connecter à Google.
  - Si l’utilisateur invité est déjà connecté à Google, il sera invité à choisir le compte qu’il souhaite utiliser. Il doit choisir le compte que vous avez utilisé pour l’inviter.

Si l’utilisateur invité voit une erreur « en-tête trop long », il peut essayer d’effacer ses cookies, ou ouvrir une fenêtre privée ou bien incognito et essayer de se reconnecter.

![Capture d’écran montrant la page de connexion Google](media/google-federation/google-sign-in.png)

## <a name="limitations"></a>Limites

Teams prend entièrement en charge les utilisateurs invités Google sur tous les appareils. Les utilisateurs de Google peuvent se connecter à Teams à partir d’un point de terminaison commun, comme `https://teams.microsoft.com`.

Les points de terminaison communs d’autres applications ne prennent pas forcément en charge les utilisateurs Google. Les utilisateurs invités Google doivent se connecter à l’aide d’un lien comportant les informations de votre locataire. Voici quelques exemples :
  * `https://myapps.microsoft.com/?tenantid=<your tenant id>`
  * `https://portal.azure.com/<your tenant id>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   S’ils essaient d’utiliser un lien comme `https://myapps.microsoft.com` ou `https://portal.azure.com`, les utilisateurs invités Google obtiendront une erreur.

Vous pouvez également fournir aux utilisateurs invités Google un lien direct vers une application ou une ressource, à condition qu’il comprenne les informations de votre locataire, par exemple `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`. 

## <a name="step-1-configure-a-google-developer-project"></a>Étape 1 : configurer un projet de développeur Google
Commencez par créer un projet dans la console des développeurs Google pour obtenir un ID client et une clé secrète client que vous pourrez ajouter plus tard à Azure AD. 
1. Accédez aux API Google à l’adresse https://console.developers.google.com et connectez-vous avec votre compte Google. Nous vous recommandons d’utiliser un compte Google d’équipe partagé.
2. Créer un projet : sur le tableau de bord, sélectionnez **Créer un projet**, puis **Créer**. À la page Nouveau projet, entrez un **Nom de projet**, puis sélectionnez **Créer**.
   
   ![Capture d’écran montrant une page Nouveau projet pour Google](media/google-federation/google-new-project.png)

3. Assurez-vous que votre nouveau projet est sélectionné dans le menu des projets. Ensuite, sous **API et services**, sélectionnez **Écran de consentement OAuth**.

4. Sélectionnez **Externe**, puis sélectionnez **Créer**. 
5. Dans l’**écran de consentement OAuth**, entrez un **nom d’application**. (Laissez les autres paramètres tels quels.)

   ![Capture d’écran montrant l’option Écran de consentement OAuth de Google](media/google-federation/google-oauth-consent-screen.png)

6. Accédez à la section **Domaines autorisés** et entrez microsoftonline.com.

   ![Capture d’écran montrant la section Domaines autorisés](media/google-federation/google-oauth-authorized-domains.png)

7. Sélectionnez **Enregistrer**.

8. Choisissez **Informations d’identification**. Dans le menu **Créer les informations d’identification**, choisissez **ID client OAuth**.

   ![Capture d’écran montrant l’option Créer les informations d’identification des API Google](media/google-federation/google-api-credentials.png)

9. Sous **Type d’application**, choisissez **Application web**, puis sous **URI de redirection autorisée**, entrez les URI suivants :
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(où `<directory id>` est votre ID de répertoire)
   
     > [!NOTE]
     > Pour trouver votre ID de répertoire, accédez à https://portal.azure.com, puis, sous **Azure Active Directory**, choisissez **Propriétés** et copiez l’**ID de répertoire**.

   ![Capture d’écran montrant la section URI de redirection autorisée](media/google-federation/google-create-oauth-client-id.png)

10. Sélectionnez **Create** (Créer). Copiez l’ID client et la clé secrète client, que vous utiliserez lors de l’ajout du fournisseur d'identité dans le portail Azure AD.

   ![Capture d’écran montrant l’ID client et la clé secrète client OAuth](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Étape 2 : configurer la fédération de Google dans Azure AD 
Maintenant vous définirez l’ID client Google et la clé secrète client, soit en les entrant dans le portail Azure AD, soit à l’aide de PowerShell. Pensez à tester votre configuration de fédération de Google en invitant vous-même à l’aide d’une adresse Gmail et en essayant d’utiliser l’invitation avec votre compte d’invité Google. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>Pour configurer la fédération de Google dans le portail Azure AD 
1. Accédez au [portail Azure](https://portal.azure.com). Sélectionnez **Azure Active Directory** dans le volet de gauche. 
2. Sélectionnez **Organizational Relationships** (norme américaine pour les relations organisationnelles).
3. Sélectionnez **Fournisseurs d’identité**, puis cliquez sur le bouton **Google**.
4. Entrez un nom. Entrez ensuite l’ID client et la clé secrète client obtenus précédemment. Sélectionnez **Enregistrer**. 

   ![Capture d’écran montrant la page d’ajout de fournisseur d’identité Google](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>Pour configurer la fédération de Google à l’aide de PowerShell
1. Installez la dernière version d’Azure AD PowerShell pour le module Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Exécutez la commande suivante : `Connect-AzureAD`.
3. À l’invite de connexion, connectez-vous avec le compte d’administrateur général géré.  
4. Exécutez la commande suivante : 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Utilisez l’ID client et la clé secrète client à partir de l’application créée à l’« étape 1 : configurer un projet de développeur Google. » Pour plus d’informations, consultez l’article [New-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview). 
 
## <a name="how-do-i-remove-google-federation"></a>Comment supprimer la fédération de Google ?
Vous pouvez supprimer votre configuration de fédération de Google. Dans ce cas, les utilisateurs invités de Google ayant déjà utilisé leur invitation ne pourront pas se connecter, mais vous pourrez leur redonner accès à vos ressources en les supprimant du répertoire, puis en les invitant à nouveau. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>Pour supprimer la fédération de Google dans le portail Azure AD : 
1. Accédez au [portail Azure](https://portal.azure.com). Sélectionnez **Azure Active Directory** dans le volet de gauche. 
2. Sélectionnez **Organizational Relationships** (norme américaine pour les relations organisationnelles).
3. Sélectionnez **Fournisseurs d’identité**.
4. Dans la ligne **Google**, sélectionnez le menu contextuel ( **...** ), puis choisissez **Supprimer**. 
   
   ![Capture d’écran montrant l’option de suppression du fournisseur d’identité sociale](media/google-federation/google-social-identity-providers.png)

1. Sélectionnez **Oui** pour confirmer la suppression. 

### <a name="to-delete-google-federation-by-using-powershell"></a>Pour supprimer la fédération de Google à l’aide de PowerShell : 
1. Installez la dernière version d’Azure AD PowerShell pour le module Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Exécutez `Connect-AzureAD`.  
4. Dans l’invite de connexion, connectez-vous avec le compte d’administrateur général géré.  
5. Entrez la commande suivante :

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Pour plus d’informations, consultez [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 
