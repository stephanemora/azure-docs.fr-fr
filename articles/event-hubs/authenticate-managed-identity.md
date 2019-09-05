---
title: Authentification d’une identité managée avec Azure Active Directory
description: Cet article fournit des informations sur l’authentification d’une identité managée avec Azure Active Directory pour accéder aux ressources Azure Event Hubs.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: cbd7de7d526e1954aaad60f7d71e5cdf202f6a29
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992457"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Authentifier une identité managée avec Azure Active Directory pour accéder aux ressources Event Hubs
Azure Event Hubs prend en charge l’authentification Azure Active Directory (Azure AD) avec des [identités managées pour ressources Azure](../active-directory/managed-identities-azure-resources/overview.md). Les identités managées pour ressources Azure peuvent autoriser l’accès à des ressources Event Hubs en utilisant les informations d’identification Azure AD d’applications s’exécutant dans des machines virtuelles Azure, des applications de fonction, le service Virtual Machine Scale Sets et d’autres services. En utilisant des identités managées pour ressources Azure et Azure AD Authentication, vous pouvez éviter de stocker des informations d’identification avec les applications qui s’exécutent dans le cloud.

Cet article montre comment autoriser l’accès à un hub d’événements en utilisant une identité managée à partir d’une machine virtuelle Azure.

## <a name="enable-managed-identities-on-a-vm"></a>Activer les identités managées sur une machine virtuelle
Avant de pouvoir utiliser les identités managées pour ressources Azure en vue d’autoriser l’accès aux ressources Event Hubs à partir de votre machine virtuelle, vous devez les activer sur la machine virtuelle. Pour savoir comment activer des identités managées pour ressources Azure, consultez un de ces articles :

- [Portail Azure](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interface de ligne de commande Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modèle Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliothèques clientes Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Octroyer des autorisations à une identité managée dans Azure AD
Pour autoriser une requête auprès du service Event Hubs à partir d’une identité managée dans votre application, commencez par configurer les paramètres du contrôle d’accès en fonction du rôle (RBAC) pour cette identité managée. Azure Event Hubs définit les rôles RBAC qui englobent les autorisations d’envoi et de lecture à partir d’Event Hubs. Quand le rôle RBAC est attribué à une identité managée, celle-ci est autorisée à accéder aux données Event Hubs selon l’étendue appropriée.

Pour plus d’informations sur l’attribution de rôles RBAC, consultez [Autoriser l’accès aux ressources Event Hubs à l’aide d’Azure Active Directory](authorize-access-azure-active-directory.md).

## <a name="use-event-hubs-with-managed-identities"></a>Utiliser Event Hubs avec des identités managées
Pour utiliser Event Hubs avec des identités managées, vous devez attribuer le rôle et l’étendue appropriés à l’identité. La procédure décrite dans cette section utilise une application simple qui s’exécute sous une identité managée et accède aux ressources Event Hubs.

Ici, nous utilisons un exemple d’application web hébergée dans [Azure App Service](https://azure.microsoft.com/services/app-service/). Pour obtenir des instructions pas à pas sur la création d’une application web, consultez [Créer une application web ASP.NET Core dans Azure](../app-service/app-service-web-get-started-dotnet.md)

Une fois que vous avez créé l’application, suivez ces étapes : 

1. Accédez à **Paramètres** et sélectionnez **Identité**. 
1. Définissez **État** sur **Activé**. 
1. Sélectionnez **Enregistrer** pour enregistrer le paramètre. 

    ![Identité managée pour une application web](./media/authenticate-managed-identity/identity-web-app.png)

Une fois ce paramètre activé, une identité de service est créée dans votre annuaire Azure Active Directory (Azure AD) et configurée dans l’hôte App Service.

À présent, attribuez cette identité de service à un rôle dans l’étendue requise dans vos ressources Event Hubs.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>Pour attribuer des rôles RBAC à l’aide du portail Azure
Pour attribuer un rôle aux ressources Event Hubs, accédez à cette ressource dans le portail Azure. Affichez les paramètres Contrôle d’accès (IAM) pour la ressource et suivez ces instructions pour gérer les attributions de rôle :

> [!NOTE]
> Les étapes suivantes vous permettent d’attribuer un rôle d’identité de service à vos espaces de noms Event Hubs. Vous pouvez suivre les mêmes étapes pour attribuer un rôle étendu à n’importe quelle ressource Event Hubs. 

1. Dans le portail Azure, accédez à votre espace de noms Event Hubs, puis affichez la **vue d’ensemble** de l’espace de noms. 
1. Sélectionnez **Contrôle d’accès (IAM)** dans le menu de gauche pour afficher les paramètres du contrôle d’accès pour le hub d’événements.
1.  Sélectionnez l’onglet **Attributions de rôles** pour afficher la liste des attributions de rôles.
3.  Sélectionnez **Ajouter** pour ajouter un nouveau rôle.
4.  Dans la page **Ajouter une attribution de rôle**, sélectionnez les rôles Event Hubs que vous souhaitez attribuer. Recherchez ensuite l’identité de service que vous avez inscrite pour attribuer le rôle.
    
    ![Page Ajouter une attribution de rôle](./media/authenticate-managed-identity/add-role-assignment-page.png)
5.  Sélectionnez **Enregistrer**. L’identité à laquelle vous avez attribué le rôle apparaît sous ce dernier. Par exemple, l’image suivante montre que l’identité de service a le rôle Propriétaire des données Azure Event Hubs.
    
    ![Identité attribuée à un rôle](./media/authenticate-managed-identity/role-assigned.png)

Une fois que vous avez attribué le rôle, l’application web a accès aux ressources Event Hubs sous l’étendue définie. 

### <a name="test-the-web-application"></a>Tester l’application web
Vous pouvez maintenant lancer votre application web et faire pointer votre navigateur vers l’exemple de page aspx. Vous trouverez l’exemple d’application web qui envoie des données aux ressources Event Hubs et reçoit des données de ces ressources dans le [dépôt GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp).

Installez le package [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) le plus récent et commencez à envoyer des données aux ressources Event Hubs et à recevoir des données de ces ressources en utilisant EventHubClient comme dans le code suivant : 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```

## <a name="next-steps"></a>Étapes suivantes
- Téléchargez l’[exemple](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp) à partir de GitHub.
- Consultez l’article suivant pour en savoir plus sur les identités managées pour ressources Azure : [Que sont les identités managées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md)
- Consultez les articles associés suivants :
    - [Authentifier les requêtes adressées à Azure Event Hubs à partir d’une application à l’aide d’Azure Active Directory](authenticate-application.md)
    - [Authentifier les requêtes adressées à Azure Event Hubs à l’aide de signatures d’accès partagé](authenticate-shared-access-signature.md)
    - [Autoriser l’accès aux ressources Event Hubs à l’aide d’Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Authentifier l’accès aux ressources Event Hubs avec des signatures d’accès partagé](authorize-access-shared-access-signature.md)