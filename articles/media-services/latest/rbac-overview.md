---
title: Contrôle d’accès en fonction du rôle pour les comptes Media Services - Azure | Microsoft Docs
description: Cet article aborde le contrôle d’accès en fonction du rôle (RBAC) pour les comptes Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: d6bc37a8aaddfb48e6d06eb46d9c1648e815b5ad
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89289237"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Contrôle d’accès en fonction du rôle (RBAC) pour les comptes Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

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

L’article [Définitions de rôle intégrés](../../role-based-access-control/built-in-roles.md) vous indique exactement ce que le rôle accorde. 

Pour plus d’informations, consultez les articles suivants :

- [Rôles Administrateur d’abonnement classique, rôles Azure et rôles Administrateur Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Qu’est-ce que le contrôle d’accès en fonction du rôle Azure (RBAC Azure) ?](../../role-based-access-control/overview.md)
- [Utiliser RBAC pour gérer un accès](../../role-based-access-control/role-assignments-rest.md)
- [Opérations de fournisseur de ressources Media Services](../../role-based-access-control/resource-provider-operations.md#microsoftmedia)

## <a name="next-steps"></a>Étapes suivantes

- [Développement avec des API Media Services v3](media-services-apis-overview.md)
- [Obtenir une stratégie de clé de contenu à l’aide de Media Services .NET](get-content-key-policy-dotnet-howto.md)
