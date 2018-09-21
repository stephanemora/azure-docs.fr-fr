---
title: Ajouter Google comme fournisseur d'identité pour Azure Active Directory B2B | Microsoft Docs
description: Fédérer avec Google pour permettre à des utilisateurs invités de se connecter à vos applications Azure AD avec leur propre compte Gmail
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: mal
ms.openlocfilehash: 4ffe6cf3f1da4c149d1cb39856d02fc40acd20cf
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984790"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Ajouter Google comme fournisseur d’identité pour les utilisateurs invités B2B

En configurant la fédération avec Google, vous pouvez autoriser des utilisateurs invités à se connecter à vos applications et ressources partagées avec leurs propres comptes Google, sans devoir créer de comptes Microsoft (MSA) ou Azure AD.  
> [!NOTE]
> Vos utilisateurs invités de Google doivent se connecter à l’aide d’un lien vers le contexte client, par exemple `https://myapps.microsoft.com/?tenantid=<tenant id>`. Liens directs aux applications et ressources fonctionnent également tant qu’ils incluent le contexte client. Les utilisateurs invités ne peuvent actuellement pas se connecter à l’aide de points de terminaison qui n’ont aucun contexte locataire. Par exemple, l’utilisation de `https://myapps.microsoft.com`, de `https://portal.azure.com` ou du point de terminaison commun des équipes entraînera une erreur.
 
## <a name="what-is-the-experience-for-the-google-user"></a>Quelle est l’expérience de l’utilisateur Google ?
Lorsque vous envoyez une invitation à un utilisateur de Google Gmail, l’utilisateur invité doit accéder à vos applications ou ressources partagées à l’aide d’un lien qui inclut le contexte locataire. Son expérience varie selon qu’il est ou non déjà connecté à Google :
  - Si l’utilisateur invité n’est pas connecté à Google, il est invité à se connecter à Google.
  - Si l’utilisateur invité est déjà connecté à Google, il sera invité à choisir le compte qu’il souhaite utiliser. Il doit choisir le compte que vous avez utilisé pour l’inviter.

Si l’utilisateur invité voit une erreur « en-tête trop long », il peut essayer d’effacer ses cookies, ou ouvrir une fenêtre privée ou bien incognito et essayer de se reconnecter.

![Se connecter avec Google](media/google-federation/google-sign-in.png)

## <a name="step-1-configure-a-google-developer-project"></a>Étape 1 : configurer un projet de développeur Google
Commencez par créer un projet dans la console des développeurs Google pour obtenir un ID client et une clé secrète client que vous pourrez ajouter plus tard à Azure AD. 
1. Accédez aux API Google à l’adresse https://console.developers.google.com et connectez-vous avec votre compte Google. Nous vous recommandons d’utiliser un compte Google d’équipe partagé.
2. Créer un projet : sur le tableau de bord, sélectionnez **Créer un projet**, puis **Créer**. À la page Nouveau projet, entrez un **Nom de projet**, puis sélectionnez **Créer**.
   
   ![Nouveau projet Google](media/google-federation/google-new-project.png)

3. Assurez-vous que votre nouveau projet est sélectionné dans le menu des projets. Ouvrez ensuite le menu en haut à gauche et sélectionnez **API et Services** > **Informations d’identification**.

   ![Informations d'identification de l’API Google](media/google-federation/google-api.png)
 
4. Choisissez l’onglet **Écran de consentement Oauth** et entrez un **Nom de produit présenté aux utilisateurs**. (Laissez les autres paramètres tels quels.) Sélectionnez **Enregistrer**.

   ![Écran de consentement Google OAuth](media/google-federation/google-oauth-consent-screen.png)

5. Choisissez l’onglet **Informations d’identification**. Dans le menu **Créer les informations d’identification**, choisissez **ID client OAuth**.

   ![Informations d'identification de l’API Google](media/google-federation/google-api-credentials.png)

6. Sous **Type d’application**, choisissez **Application web**, puis sous **URI de redirection autorisée**, entrez les URI suivants :
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(où `<directory id>` est votre ID de répertoire)
   
    > [!NOTE]
    > Pour trouver votre ID de répertoire, accédez à https://portal.azure.com, puis, sous **Azure Active Directory**, choisissez **Propriétés** et copiez l’**ID de répertoire**.

   ![Créer un ID client OAuth](media/google-federation/google-create-oauth-client-id.png)

7. Sélectionnez **Créer**. Copiez l’ID client et la clé secrète client, que vous utiliserez lors de l’ajout du fournisseur d'identité dans le portail Azure AD.

   ![ID client OAuth et clé secrète client](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Étape 2 : configurer la fédération de Google dans Azure AD 
Maintenant vous définirez l’ID client Google et la clé secrète client, soit en les entrant dans le portail Azure AD, soit à l’aide de PowerShell. Pensez à tester votre configuration de fédération de Google en invitant vous-même à l’aide d’une adresse Gmail et en essayant d’utiliser l’invitation avec votre compte d’invité Google. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>Pour configurer la fédération de Google dans le portail Azure AD 
1. Accédez au [portail Azure](https://portal.azure.com). Sélectionnez **Azure Active Directory** dans le volet de gauche. 
2. Sélectionnez **Organizational Relationships** (norme américaine pour les relations organisationnelles).
3. Sélectionnez **Fournisseurs d’identité**, puis cliquez sur le bouton **Google**.
4. Entrez un nom. Entrez ensuite l’ID client et la clé secrète client obtenus précédemment. Sélectionnez **Enregistrer**. 

   ![Ajouter le fournisseur d’identité Google](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>Pour configurer la fédération de Google à l’aide de PowerShell
1. Installez la dernière version d’Azure AD PowerShell pour le module Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Exécutez la commande suivante : `Connect-AzureAD`.
3. À l’invite de connexion, connectez-vous avec le compte d’administrateur général géré.  
4. Exécutez la commande suivante : 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Utilisez l’ID client et la clé secrète client à partir de l’application créée à l’« étape 1 : configurer un projet de développeur Google. » Pour plus d’informations, consultez l’article [New-AzureADMSIdentityProvider](https://docs.microsoft.com/en-us/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview). 
 
## <a name="how-do-i-remove-google-federation"></a>Comment supprimer la fédération de Google ?
Vous pouvez supprimer votre configuration de fédération de Google. Dans ce cas, les utilisateurs invités de Google ayant déjà utilisé leur invitation ne pourront pas se connecter, mais vous pourrez leur redonner accès à vos ressources en les supprimant du répertoire, puis en les invitant à nouveau. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>Pour supprimer la fédération de Google dans le portail Azure AD : 
1. Accédez au [portail Azure](https://portal.azure.com). Sélectionnez **Azure Active Directory** dans le volet de gauche. 
2. Sélectionnez **Organizational Relationships** (norme américaine pour les relations organisationnelles).
3. Sélectionnez **Fournisseurs d’identité**, puis cliquez sur le bouton **Google**.
4. Sélectionnez **Google**, puis **Supprimer**. 
   
   ![Supprimer le fournisseur d’identité sociale](media/google-federation/google-social-identity-providers.png)

1. Sélectionnez **Oui** pour confirmer la suppression. 

### <a name="to-delete-google-federation-by-using-powershell"></a>Pour supprimer la fédération de Google à l’aide de PowerShell : 
1. Installez la dernière version d’Azure AD PowerShell pour le module Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Exécutez `Connect-AzureAD`.  
4. Dans l’invite de connexion, connectez-vous avec le compte d’administrateur général géré.  
5. Entrez la commande suivante :

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Pour plus d’informations, consultez [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/en-us/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 