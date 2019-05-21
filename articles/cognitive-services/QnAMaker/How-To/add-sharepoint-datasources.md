---
title: Fichiers SharePoint - QnA Maker
titleSuffix: Azure Cognitive Services
description: Ajouter des sources de données Sharepoint sécurisés à votre base de connaissances pour enrichir la base de connaissances avec questions et réponses qui peuvent être sécurisés avec Active Directory.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/05/2019
ms.author: tulasim
ms.openlocfilehash: e479cf1729b7dcd2ed2f2470f2a935bdf94af80b
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65954955"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>Ajouter une source de données Sharepoint sécurisée à votre base de connaissances

Ajouter des sources de données Sharepoint sécurisés à votre base de connaissances pour enrichir la base de connaissances avec questions et réponses qui peuvent être sécurisés avec Active Directory. 

Lorsque vous ajoutez un document Sharepoint sécurisé à votre base de connaissances, comme le Gestionnaire de QnA Maker, vous devez demander l’autorisation d’Active Directory pour QnA Maker. Une fois que cette autorisation est accordée à partir du Gestionnaire d’Active Directory à QnA Maker pour l’accès à Sharepoint, il ne doit être donnée à nouveau. Chaque ajout de document suivants à la base de connaissances n’a pas besoin d’autorisation s’il s’agit de la même ressource de Sharepoint. 

Si le Gestionnaire de base de connaissances QnA Maker n’est pas le Gestionnaire d’Active Directory, vous devrez communiquer avec le Gestionnaire de Active Directory pour terminer ce processus.

## <a name="add-supported-file-types-to-knowledge-base"></a>Ajouter des types de fichiers pris en charge pour la base de connaissances

Vous pouvez ajouter tout pris en charge QnA Maker [types de fichiers](../Concepts/data-sources-supported.md) à partir d’un serveur Sharepoint à votre base de connaissances. Vous devrez peut-être accorder [autorisations](#permissions) si la ressource de fichier est sécurisée.

1. À partir du serveur Sharepoint, sélectionnez le menu de points de suspension du fichier, `...`.
1. Copiez l’URL du fichier.

    ![Obtenir l’URL du fichier Sharepoint en sélectionnant le menu de points de suspension du fichier puis en copiant l’URL.](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. Dans le portail QnA Maker, sur le **paramètres** page, [ajouter l’URL](edit-knowledge-base.md#add-datasource) à la base de connaissances. 

## <a name="permissions"></a>Autorisations

Octroi d’autorisations se produit lorsqu’un fichier sécurisé à partir d’un serveur Sharepoint est ajouté à une base de connaissances. Selon la configuration de Sharepoint à distance et les autorisations de la personne qui ajoute le fichier, cela peut nécessiter :

* Aucune étape supplémentaire - la personne qui ajoute le fichier a toutes les autorisations nécessaires.
* étapes à la fois par [manager de la base de connaissances](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal) et [manager d’Active Directory](#active-directory-manager-grant-file-read-access-to-qna-maker).

Consultez les étapes répertoriées ci-dessous. 

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>Gestionnaire de la base de connaissances : ajouter la source de données Sharepoint dans le portail QnA Maker

Lorsque le **manager de QnA Maker** ajoute un document sécurisé de Sharepoint à une base de connaissances, la base de connaissances manager lance une demande d’autorisation que le Gestionnaire d’Active Directory a besoin pour terminer.

La requête commence par une fenêtre contextuelle pour s’authentifier auprès d’un compte Active Directory. 

![Authentifier le compte d’utilisateur](../media/add-sharepoint-datasources/authenticate-user-account.png)

Une fois que le Gestionnaire de QnA Maker sélectionne le compte, l’administrateur Active Directory recevra une notification qu’ils doivent autoriser l’accès de l’application (pas le Gestionnaire de QnA Maker) à la ressource Sharepoint QnA Maker. Le Gestionnaire d’Active Directory devez procéder ainsi pour chaque ressource de Sharepoint, mais pas tous les documents dans cette ressource. 

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Gestionnaire Active directory : accorder l’accès en lecture de fichier à QnA Maker

Le Gestionnaire d’Active Directory (pas le Gestionnaire de QnA Maker) doit accorder l’accès à l’API QnA Maker pour accéder à la ressource de Sharepoint en sélectionnant [ce lien](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68) pour autoriser l’application d’entreprise Sharepoint du portail QnA Maker pour que la lecture du fichier autorisations. 

![Le gestionnaire Azure Active Directory accorde l’autorisation interactive](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharepoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`. 
-->
<!--
### Grant access from the interactive pop-up window 

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharepoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharepoint**, and a list of permissions requested. Select **Accept** to provide those permissions. 

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**. 

1. Search for and select the **QnAMakerPortalSharepoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

    ![Select QnA Maker app in App registrations list](../media/add-sharepoint-datasources/select-qna-maker-app-in-app-registrations.png)

1. Select **Settings**.

    [![Select Settings in the right-side blade](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png)](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png#lightbox)

1. Under **API access**, select **Required permissions**. 

    ![Select 'Settings', then under 'API access', select 'Required permission'](../media/add-sharepoint-datasources/select-required-permissions-in-settings-blade.png)

1. Do not change any settings in the **Enable Access** window. Select **Grant Permission**. 

    [![Under 'Grant Permission', select 'Yes'](../media/add-sharepoint-datasources/grant-app-required-permissions.png)](../media/add-sharepoint-datasources/grant-app-required-permissions.png#lightbox)

1. Select **YES** in the pop-up confirmation windows. 

    ![Grant required permissions](../media/add-sharepoint-datasources/grant-required-permissions.png)
-->
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>Accorder l’accès depuis le centre d’administration Azure Active Directory

1. Le Gestionnaire d’Active Directory se connecte au portail Azure et ouvre  **[applications d’entreprise](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)**. 

1. Recherchez `QnAMakerPortalSharepoint` le, sélectionnez l’application de QnA Maker. 

    [![Recherchez QnAMakerPortalSharepoint dans la liste des applications d’entreprise](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. Sous **sécurité**, accédez à **autorisations**. Sélectionnez **accorder le consentement de l’administrateur pour l’organisation**. 

    [![Sélectionner un utilisateur authentifié pour l’administrateur Active Directory](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. Sélectionnez un compte d’authentification avec des autorisations pour accorder des autorisations pour l’annuaire Active Directory. 


  
<!--

## Add Sharepoint data source with APIs

You need to get the Sharepoint file's URI before adding it to QnA Maker. 

## Get Sharepoint File URI

Use the following steps to transform the Sharepoint URL into a sharing token.

1. Encode the URL using [base64](https://en.wikipedia.org/wiki/Base64). 

1. Convert the base64-encoded result to an unpadded base64url format with the following character changes. 

    * Remove the equal character, `=` from the end of the value. 
    * Replace `/` with `_`. 
    * Replace `+` with `-`. 
    * Append `u!` to be beginning of the string. 

1. Sign in to Graph explorer and run the following query, where `sharedURL` is ...:

    ```
    https://graph.microsoft.com/v1.0/shares/<sharedURL>/driveitem
    ```

    Get the **@microsoft.graph.downloadUrl** and use this as `fileuri` in the QnA Maker APIs.

### Add or update a Sharepoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](https://go.microsoft.com/fwlink/?linkid=2092179) or [updating a knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<sharepoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from Sharepoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center. 
1. When you select the **QnAMakerPortalSharepoint**, select **Overview**. 
1. Select **Delete** to remove permissions. 

-->

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Collaborer sur votre base de connaissances](collaborate-knowledge-base.md)
