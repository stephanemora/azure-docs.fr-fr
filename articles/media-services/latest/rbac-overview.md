---
title: Le contrôle d’accès en fonction du rôle pour les comptes Media Services - Azure | Microsoft Docs
description: Cet article décrit le contrôle d’accès en fonction du rôle (RBAC) pour les comptes Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 82c438ea246ba18fa7dac2281d68a3ea7ba41db8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60930198"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Contrôle d’accès en fonction du rôle (RBAC) pour les comptes Media Services

Actuellement, Azure Media Services ne définit pas les rôles personnalisés spécifiques au service. Pour obtenir un accès complet au compte Media Services, les clients peuvent utiliser les rôles intégrés de **propriétaire** ou **contributeur**. La principale différence entre ces rôles est : le **propriétaire** pouvez contrôler qui a accès à une ressource et le **contributeur** ne peut pas. Intégrés **lecteur** rôle peut également être utilisé, mais l’utilisateur ou l’application aura uniquement un accès en lecture à l’API Media Services. 

## <a name="design-principles"></a>Principes de conception

L’un des principes de conception clés de l’API v3 est de renforcer la sécurité de l’API. les API v3 ne retournent pas de secrets ou des informations d’identification sur **obtenir** ou **liste** operations. Les clés sont toujours null, vides ou purgées de la réponse. L’utilisateur doit appeler une méthode d’action séparée pour obtenir des informations d’identification ou des secrets. Le **lecteur** rôle ne peut pas appeler des opérations telles que Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets. Ayant des actions distinctes vous permet de définir des autorisations de sécurité plus granulaires de RBAC dans un rôle personnalisé si vous le souhaitez.

Pour répertorier les opérations de Media Services prend en charge, procédez comme :

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

Le [des définitions de rôle intégré](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) article vous indique exactement ce que le rôle accorde. 

Pour plus d’informations, consultez les articles suivants :

- [Rôles d’administrateur d’abonnement classique, rôles RBAC Azure et rôles d’administrateur Azure AD](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [Qu’est RBAC pour les ressources Azure ?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Utilisez RBAC pour gérer l’accès](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)

## <a name="next-steps"></a>Étapes suivantes

- [Développement avec Media Services v3 API](media-services-apis-overview.md)
- [Obtenir la stratégie de clé de contenu à l’aide de Media Services .NET](get-content-key-policy-dotnet-howto.md)
