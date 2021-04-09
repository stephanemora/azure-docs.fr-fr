---
title: Authentifier une application pour accéder à Azure SignalR Service
description: Cet article fournit des informations sur l’authentification d’une application avec Azure Active Directory pour accéder à Azure SignalR Service
author: terencefan
ms.author: tefa
ms.service: signalr
ms.topic: conceptual
ms.date: 08/03/2020
ms.openlocfilehash: 597b69c1180ea1fb2a6812d648f8b8ad37707d66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092604"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-azure-signalr-service"></a>Authentifier une application avec Azure Active Directory pour accéder à Azure SignalR Service
Microsoft Azure offre la gestion du contrôle d’accès intégré pour les ressources et les applications basées sur Azure Active Directory (Azure AD). L’un des principaux avantages de l’utilisation d’Azure AD avec Azure SignalR Service est que vous n’avez plus besoin de stocker vos informations d’identification dans le code. Au lieu de cela, vous pouvez demander un jeton d’accès OAuth 2.0 à partir de la Plateforme d’identité Microsoft. Le nom de ressource à utiliser pour demander un jeton est `https://signalr.azure.com/`. Azure AD authentifie le principal de sécurité (une application, un groupe de ressources ou un principal de service) qui exécute l’application. Si l’authentification réussit, Azure AD retourne un jeton d’accès à l’application. Cette dernière peut ensuite utiliser le jeton d’accès pour autoriser les demandes d’accès aux ressources Azure SignalR Service.

Lorsqu’un rôle est attribué à un principal de sécurité Azure AD, Azure octroie l’accès à ces ressources pour ce principal de sécurité. L’étendue de l’accès peut être limité au niveau de l’abonnement, du groupe de ressources ou de la ressource Azure SignalR. Un principal de sécurité Azure AD peut attribuer des rôles à un utilisateur, à un groupe, à un principal de service d’application ou à une [identité managée pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md). 

> [!NOTE]
> Une définition de rôle est une collection d’autorisations. Le contrôle d’accès en fonction du rôle (RBAC) contrôle la façon dont ces autorisations sont appliquées par le biais de l’attribution de rôle. Une attribution de rôle se compose de trois éléments : un principal de sécurité, une définition de rôle et une étendue. Pour plus d’informations, consultez [Comprendre les différents rôles](../role-based-access-control/overview.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Inscrire votre application à un locataire Azure AD
La première étape de l’utilisation d’Azure AD pour autoriser l’accès aux ressources Azure SignalR Service consiste à inscrire votre application cliente auprès d’un locataire Azure AD à partir du [portail Azure](https://portal.azure.com/). Quand vous inscrivez votre application cliente, vous fournissez des informations la concernant à Azure AD. Azure AD fournit ensuite un ID de client (appelé aussi ID d’application) que vous pouvez utiliser pour associer votre application au runtime Azure AD. Pour en savoir plus sur l’ID de client, consultez [Objets application et principal du service dans Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

Les illustrations suivantes montrent les étapes d’inscription d’une application web :

![Inscrire une application](./media/authenticate/app-registrations-register.png)

> [!Note]
> Si vous inscrivez votre application comme une application native, vous pouvez spécifier n’importe quel URI valide pour l’URI de redirection. Pour les applications natives, cette valeur ne devra pas être une URL réelle. Pour les applications web, l’URI de redirection doit être un URI valide, car il spécifie l’URL à laquelle les jetons sont fournis.

Une fois votre application inscrite, **l’ID (client) d’application** se trouve sous **Paramètres** :

![ID de l’application inscrite](./media/authenticate/application-id.png)

Pour plus d’informations sur l’inscription d’une application dans Azure AD, consultez [Intégration d’applications à Azure Active Directory](../active-directory/develop/quickstart-register-app.md).


### <a name="create-a-client-secret"></a>Créer une clé secrète client   
L’application a besoin d’une clé secrète client pour prouver son identité lors de la requête de jeton. Pour ajouter le secret client, effectuez ces étapes.

1. Accédez à votre inscription d’application dans le Portail Microsoft Azure.
1. Sélectionnez le paramètre **Certificats et clés secrètes**.
1. Sous **Secrets client**, sélectionnez **Nouveau secret client** pour créer un secret.
1. Fournissez une description pour le secret, puis choisissez un intervalle d’expiration.
1. Copiez immédiatement la valeur de la nouvelle clé secrète dans un emplacement sécurisé. La valeur de remplissage s’affiche pour vous une seule fois.

![Créer un secret client](./media/authenticate/client-secret.png)

### <a name="upload-a-certificate"></a>Téléchargement d'un certificat

Vous pouvez également charger une certification au lieu de créer un secret client.

![Charger une certification](./media/authenticate/certification.png)

## <a name="assign-azure-roles-using-the-azure-portal"></a>Attribuer des rôles Azure à l’aide du portail Azure  
Pour en savoir plus sur la gestion de l’accès aux ressources Azure à l’aide d’Azure RBAC et du portail Azure, consultez [cet article](..//role-based-access-control/role-assignments-portal.md). 

Après avoir déterminé l’étendue appropriée pour une attribution de rôle, accédez à cette ressource dans le portail Azure. Affichez les paramètres Contrôle d’accès (IAM) pour la ressource et suivez ces instructions pour gérer les attributions de rôle :

1. Dans le [portail Azure](https://portal.azure.com/), accédez à votre ressource SignalR.
1. Sélectionnez **Contrôle d’accès (IAM)** pour afficher les paramètres de contrôle d’accès d’Azure SignalR. 
1. Sélectionnez l’onglet **Attributions de rôles** pour afficher la liste des attributions de rôles. Sélectionnez le bouton **Ajouter** dans la barre d’outils, puis sélectionnez **Ajouter une attribution de rôle**. 

    ![Ajouter un bouton à la barre d’outils](./media/authenticate/role-assignments-add-button.png)

1. Sur la page **Ajouter une attribution de rôle**, procédez comme suit :
    1. Sélectionnez le **rôle Azure SignalR** à attribuer. 
    1. Recherchez le **principal de sécurité** (utilisateur, groupe, principal de service) auquel vous souhaitez attribuer le rôle.
    1. Sélectionnez **Enregistrer** pour enregistrer l’attribution de rôle. 

        ![Attribuer un rôle à une application](./media/authenticate/assign-role-to-application.png)

    1. L’identité à laquelle vous avez attribué le rôle apparaît sous ce dernier. Par exemple, l’image suivante montre que les applications `signalr-dev` et `signalr-service` se trouvent dans le rôle serveur d’applications SignalR. 
        
        ![Liste d’attributions de rôles](./media/authenticate/role-assignment-list.png)

Vous pouvez suivre des étapes similaires pour attribuer un rôle limité à l’étendue d’un groupe de ressources ou d’un abonnement. Une fois que vous avez défini le rôle et son étendue, vous pouvez tester ce comportement à l’aide d’exemples [dans cet emplacement GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac).

## <a name="sample-codes-while-configuring-your-app-server"></a>Exemples de code pour la configuration de votre serveur d’applications.

Ajoutez les options suivantes avec `AddAzureSignalR` :

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientSecret=<clientSecret>;tenantId=<tenantId>";
});
```

Ou configurez simplement `ConnectionString` dans votre fichier `appsettings.json`.

```json
{
"Azure": {
    "SignalR": {
      "Enabled": true,
      "ConnectionString": "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientSecret=<clientSecret>;tenantId=<tenantId>"
    }
  },
}
```

Quand vous utilisez `certificate`, remplacez `clientSecret` par `clientCert` comme ceci :

```C#
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientCert=<clientCertFilepath>;tenantId=<tenantId>";
```

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur le contrôle RBAC, consultez [Qu’est-ce que le contrôle d’accès en fonction du rôle Azure (Azure RBAC) ?](../role-based-access-control/overview.md).
- Pour apprendre à attribuer et gérer les rôles Azure avec Azure PowerShell, Azure CLI ou l’API REST, consultez les articles suivants :
    - [Gérer le contrôle d’accès en fonction du rôle (RBAC) avec Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Gérer le contrôle d’accès en fonction du rôle (RBAC) avec Azure CLI](../role-based-access-control/role-assignments-cli.md)
    - [Gérer le contrôle d’accès en fonction du rôle (RBAC) avec l’API REST](../role-based-access-control/role-assignments-rest.md)
    - [Gérer le contrôle d’accès en fonction du rôle (RBAC) avec des modèles Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

Consultez les articles associés suivants :
- [Authentifier une identité managée avec Azure Active Directory pour accéder à Azure SignalR Service](authenticate-managed-identity.md)
- [Autoriser l’accès à Azure SignalR Service avec Azure Active Directory](authorize-access-azure-active-directory.md)