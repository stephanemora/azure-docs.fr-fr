---
title: Inscrire une ressource d’application dans Azure AD – API Azure pour FHIR
description: Inscrivez une ressource d’application (ou API) dans Azure Active Directory, afin que les applications clientes puissent demander l’accès à la ressource lors de l’authentification.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: e92fab392dc73d8de0b7b2547e38b3f345562930
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91975856"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>Inscrire une ressource d'application dans Azure Active Directory

Dans cet article, vous allez découvrir comment inscrire une ressource (ou API) d'application dans Azure Active Directory. Une ressource d’application est une représentation d’Azure Active Directory de l’API du serveur FHIR et les applications clientes peuvent demander l’accès aux ressources lors de l’authentification. La ressource d'application est aussi également appelée *audience* dans OAuth.

## <a name="azure-api-for-fhir"></a>API Azure pour FHIR

Si vous utilisez l’API Azure pour FHIR, une ressource d’application est automatiquement créée lorsque vous déployez le service. Tant que vous utilisez l’API Azure pour FHIR dans le même locataire Azure Active Directory que celui qui déploie votre application, vous pouvez ignorer ce guide pratique et déployer votre API Azure pour FHIR à la place pour la prendre en main.

Si vous utilisez un autre locataire Azure Active Directory (non associé à votre abonnement), vous pouvez importer la ressource d’application API Azure pour FHIR dans votre locataire à l’aide de PowerShell :

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

Ou en utilisant Azure CLI :

```azurecli-interactive
az ad sp create --id 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

## <a name="fhir-server-for-azure"></a>Serveur FHIR pour Azure

Si vous utilisez le serveur FHIR open source pour Azure, suivez les étapes ci-dessous pour inscrire une ressource d’application.

### <a name="app-registrations-in-azure-portal"></a>Inscriptions d’applications dans le portail Azure

1. Dans le volet de navigation gauche du [portail Azure](https://portal.azure.com), cliquez sur **Azure Active Directory**.

2. Dans le panneau **Azure Active Directory**, cliquez sur **Inscriptions d’applications** :

    ![Portail Azure. Nouvelle inscription d’application.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Cliquez sur **Nouvelle inscription**.

### <a name="add-a-new-application-registration"></a>Ajouter une nouvelle inscription d’application

Renseignez les détails de la nouvelle application. Il n’existe aucune exigence particulière pour le nom d’affichage, mais l’affecter à l’URI du serveur FHIR la rend facile à trouver :

![Nouvelle inscription d’application](media/how-to-aad/portal-aad-register-new-app-registration-NAME.png)

### <a name="set-identifier-uri-and-define-scopes"></a>Définir l’URI de l’identificateur et définir les étendues

Une ressource d'application dispose d’un URI d’identificateur (URI ID d’application), que les clients peuvent utiliser lorsqu’ils demandent l’accès aux ressources. Cette valeur remplit la revendication `aud` du jeton d’accès. Il est recommandé de définir cet URI comme URI de votre serveur FHIR. Pour SMART sur des applications FHIR, nous partons du principe que l’*audience* est l’URI du serveur FHIR.

1. Cliquer sur **Exposer une API**

2. Cliquez sur **Définir** à côté de *URI ID d’application*.

3. Entrez l’URI de l’identificateur, puis cliquez sur **Enregistrer**. Un bon URI d’identificateur serait l’URI de votre serveur FHIR.

4. Cliquez sur **Ajouter une étendue** et ajoutez toutes les étendues que vous souhaitez définir pour votre API. Vous devez ajouter au moins une étendue pour pouvoir accorder des autorisations à votre ressource d’application à l’avenir. Si vous n’avez pas d’étendues spécifiques que vous souhaitez ajouter, vous pouvez ajouter user_impersonation comme étendue.

![Audience et étendue](media/how-to-aad/portal-aad-register-new-app-registration-AUD-SCOPE.png)

### <a name="define-application-roles"></a>Définir les rôles de l’application

L’API Azure pour FHIR et le serveur OSS FHIR pour Azure utilisent des [rôles d’application Azure Active Directory](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) comme contrôle d'accès en fonction du rôle. Pour définir quels rôles doivent être disponibles pour votre API de serveur FHIR, ouvrez le [manifeste](https://docs.microsoft.com/azure/active-directory/active-directory-application-manifest/) de la ressource d’application :

1. Cliquez sur **Manifeste** :

    ![Rôles d’application](media/how-to-aad/portal-aad-register-new-app-registration-APP-ROLES.png)

2. Dans la propriété `appRoles`, ajoutez les rôles que vous souhaitez pour les utilisateurs ou les applications :

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User",
          "Application"
        ],
        "description": "FHIR Server Administrators",
        "displayName": "admin",
        "id": "1b4f816e-5eaf-48b9-8613-7923830595ad",
        "isEnabled": true,
        "value": "admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "description": "Users who can read",
        "displayName": "reader",
        "id": "c20e145e-5459-4a6c-a074-b942bbd4cfe1",
        "isEnabled": true,
        "value": "reader"
      }
    ],
    ```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à inscrire une ressource d'application dans Azure Active Directory. Maintenant, inscrivez votre application cliente confidentielle.
 
>[!div class="nextstepaction"]
>[Inscrire une application cliente confidentielle](register-confidential-azure-ad-client-app.md)