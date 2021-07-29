---
title: Authentification d’une identité managée avec Azure Active Directory
description: Cet article fournit des informations sur l’authentification d’une identité managée avec Azure Active Directory pour accéder à Azure SignalR Service
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: b2766495c89b9be1a89e0648bcec83c160c1e0a8
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110479440"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-signalr-resources"></a>Authentifier une identité managée avec Azure Active Directory pour accéder aux ressources Azure SignalR
Azure SignalR Service prend en charge l’authentification Azure Active Directory (Azure AD) avec des [identités managées pour ressources Azure](../active-directory/managed-identities-azure-resources/overview.md). Les identités managées pour ressources Azure peuvent autoriser l’accès à des ressources Azure SignalR Service en utilisant les informations d’identification Azure AD d’applications s’exécutant dans des machines virtuelles Azure, des applications de fonction, le service Virtual Machine Scale Sets et d’autres services. En utilisant des identités managées pour ressources Azure et Azure AD Authentication, vous pouvez éviter de stocker des informations d’identification avec les applications qui s’exécutent dans le cloud.

Cet article montre comment autoriser l’accès à Azure SignalR Service en utilisant une identité managée.

## <a name="enable-managed-identities"></a>Activer les identités managées
Avant de pouvoir utiliser les identités managées pour ressources Azure en vue d’autoriser l’accès à Azure SignalR Service, vous devez activer les identités managées pour ressources Azure. 
### <a name="for-azure-resources-on-a-vm"></a>Pour les ressources Azure sur une machine virtuelle
Pour savoir comment activer des identités managées pour Azure Ressource sur une machine virtuelle, consultez un de ces articles :

- [Azure portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
  1. Accédez à **Paramètres** sur Portail Azure et sélectionnez **Identité**. 
  2. Définissez **État** sur **Activé**. 
  3. Sélectionnez **Enregistrer** pour enregistrer le paramètre. 

      ![Identité managée pour une machine virtuelle](./media/authenticate/identity-virtual-machine.png)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modèle Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliothèques clientes Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

### <a name="for-azure-functions"></a>Pour Azure Functions

Consultez [Guide pratique pour utiliser des identités managées avec App Service et Azure Functions](../app-service/overview-managed-identity.md).

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Octroyer des autorisations à une identité managée dans Azure AD
Pour autoriser une requête auprès d’Azure SignalR Service à partir d’une identité managée dans votre application, commencez par configurer les paramètres du contrôle d’accès en fonction du rôle (RBAC) pour cette identité managée. Azure SignalR Service définit des rôles RBAC qui englobent des autorisations pour acquérir une `AccessKey` ou un `ClientToken`. Quand le rôle RBAC est attribué à une identité managée, celle-ci est autorisée à accéder à Azure SignalR Service au niveau d’étendue approprié.

Suivez ces instructions pour gérer les attributions de rôles :

1. Dans le [portail Azure](https://portal.azure.com/), accédez à votre ressource SignalR.
1. Sélectionnez **Contrôle d’accès (IAM)** pour afficher les paramètres de contrôle d’accès d’Azure SignalR. 
1. Sélectionnez l’onglet **Attributions de rôles** pour afficher la liste des attributions de rôles. Sélectionnez le bouton **Ajouter** dans la barre d’outils, puis sélectionnez **Ajouter une attribution de rôle**. 

    ![Ajouter un bouton à la barre d’outils](./media/authenticate/role-assignments-add-button.png)

1. Sur la page **Ajouter une attribution de rôle**, procédez comme suit :
    1. Sélectionnez le **Serveur d’application SignalR** comme rôle. Notez que cela s’applique également à **Azure Functions App**.
    1. Recherchez le **principal de sécurité** (utilisateur, groupe, principal de service) auquel vous souhaitez attribuer le rôle.
    1. Sélectionnez **Enregistrer** pour enregistrer l’attribution de rôle. 

        ![Attribuer un rôle à une application](./media/authenticate/assign-role-to-application.png)

    1. L’identité à laquelle vous avez attribué le rôle apparaît sous ce dernier. Par exemple, l’image suivante montre que les applications `signalr-dev` et `signalr-service` se trouvent dans le rôle serveur d’applications SignalR. 
        
        ![Liste d’attributions de rôles](./media/authenticate/role-assignment-list.png)

Vous pouvez suivre des étapes similaires pour attribuer un rôle limité à l’étendue d’un groupe de ressources ou d’un abonnement. Une fois que vous avez défini le rôle et son étendue, vous pouvez tester ce comportement à l’aide d’exemples [dans cet emplacement GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac).

Pour plus d’informations sur l’attribution de rôles RBAC, consultez [Autoriser l’accès à Azure SignalR Service à l’aide d’Azure Active Directory](authorize-access-azure-active-directory.md).

Pour en savoir plus sur la gestion de l’accès aux ressources Azure à l’aide d’Azure RBAC et du portail Azure, consultez [cet article](..//role-based-access-control/role-assignments-portal.md). 

## <a name="configure-your-app"></a>Configuration de votre application
### <a name="app-server"></a>App Server

Ajoutez les options suivantes avec `AddAzureSignalR` :

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;Version=1.0;";
});
```

### <a name="azure-functions-app"></a>Application Azure Functions

Sur le Portail Azure, ajoutez un paramètre d’application avec le nom `AzureSignalRConnectionString` et la valeur `Endpoint=https://<name>.signalr.net;AuthType=aad;`.

Sur local, dans votre fichier `local.appsettings.json`, ajoutez la section `Values` suivante :
```json
{
    "Values": {
        "AzureSignalRConnectionString": "Endpoint=https://<name>.signalr.net;AuthType=aad;Version=1.0;"
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur le contrôle RBAC, consultez [Qu’est-ce que le contrôle d’accès en fonction du rôle Azure (Azure RBAC) ?](../role-based-access-control/overview.md).
- Pour apprendre à attribuer et gérer les rôles Azure avec Azure PowerShell, Azure CLI ou l’API REST, consultez les articles suivants :
    - [Gérer le contrôle d’accès en fonction du rôle (RBAC) avec Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Gérer le contrôle d’accès en fonction du rôle (RBAC) avec Azure CLI](../role-based-access-control/role-assignments-cli.md)
    - [Gérer le contrôle d’accès en fonction du rôle (RBAC) avec l’API REST](../role-based-access-control/role-assignments-rest.md)
    - [Gérer le contrôle d’accès en fonction du rôle (RBAC) avec des modèles Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

Consultez les articles associés suivants :
- [Authentifier une application avec Azure Active Directory pour accéder à Azure SignalR Service](authenticate-application.md)
- [Autoriser l’accès à Azure SignalR Service avec Azure Active Directory](authorize-access-azure-active-directory.md)
