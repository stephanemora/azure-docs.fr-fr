---
title: Contrôle d’accès en fonction du rôle pour les comptes Media Services - Azure | Microsoft Docs
description: Cet article aborde le contrôle d’accès en fonction du rôle (RBAC) pour les comptes Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/23/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 93b2cd3a2565b14ea07d6db6b14dd146e4223528
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "66236917"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Contrôle d’accès en fonction du rôle (RBAC) pour les comptes Media Services

Actuellement, Azure Media Services ne définit pas les rôles personnalisés spécifiques au service. Pour obtenir un accès complet au compte Media Services, les clients peuvent utiliser les rôles intégrés de **Propriétaire** ou **Contributeur**. La principale différence entre ces rôles est que le **propriétaire** peut être les personnes accédant à une ressource tandis que le **contributeur** ne peut pas. Le rôle **lecteur** intégré peut également être utilisé, mais l’utilisateur ou l’application aura uniquement un accès en lecture aux API Media Services. 

## <a name="design-principles"></a>Principes de conception

L’un des principes de conception clés de l’API v3 est de renforcer la sécurité de l’API. Les API v3 ne retournent pas de secrets ou d’informations d’identification lors des opérations **Get** ou **List**. Les clés sont toujours null, vides ou purgées de la réponse. L’utilisateur doit appeler une méthode d’action distincte pour obtenir des informations d’identification ou des secrets. Le rôle **lecteur** ne peut pas appeler d’opérations telles que Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets. Le fait d’avoir des actions distinctes vous permet de définir des autorisations de sécurité RBAC plus granulaires dans un rôle personnalisé si vous le souhaitez.

Pour répertorier les opérations prises en charge par Media Services, procédez comme :

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

L’article [Définitions de rôle intégrés](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) vous indique exactement ce que le rôle accorde. 

Pour plus d’informations, consultez les articles suivants :

- [Rôles d’administrateur d’abonnement classique, rôles RBAC Azure et rôles d’administrateur Azure AD](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [RBAC pour les ressources Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Utiliser RBAC pour gérer un accès](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Opérations de fournisseur de ressources Media Services](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftmedia)

## <a name="next-steps"></a>Étapes suivantes

- [Développement avec des API Media Services v3](media-services-apis-overview.md)
- [Obtenir une stratégie de clé de contenu à l’aide de Media Services .NET](get-content-key-policy-dotnet-howto.md)
