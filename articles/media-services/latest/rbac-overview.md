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
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 212d7f5352b58ee8f5b2c119bb1f5f828591f6bf
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471892"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Contrôle d’accès en fonction du rôle (RBAC) pour les comptes Media Services

Actuellement, Azure Media Services ne définis pas n’importe quel spécifiques à des rôles personnalisés au service. Les clients peuvent utiliser les rôles intégrés de **propriétaire** ou **contributeur** pour obtenir un accès complet à un compte Media Services. La principale différence entre ces rôles est : le **propriétaire** pouvez contrôler qui a accès à une ressource et le **contributeur** ne peut pas. Le compte de lecteur intégré a uniquement un accès en lecture au compte Media Services. 

## <a name="design-principles"></a>Principes de conception

L’un des principes de conception clés de l’API v3 est de renforcer la sécurité de l’API. les API v3 ne retournent pas de secrets ou des informations d’identification sur **obtenir** ou **liste** operations. Les clés sont toujours null, vides ou purgées de la réponse. L’utilisateur doit appeler une méthode d’action séparée pour obtenir des informations d’identification ou des secrets. Le **lecteur** rôle ne peut pas appeler des opérations de sorte qu’il ne peut pas appeler des opérations telles que Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets. Ayant des actions distinctes vous permet de définir des autorisations de sécurité plus granulaires de RBAC dans un rôle personnalisé si vous le souhaitez.

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
- [Obtenir la stratégie de clé de contenu à l’aide de Media Services .NET](get-content-key-policy-dotnet-howto.md)

## <a name="next-steps"></a>Étapes suivantes

[Développement avec Media Services v3 API](media-services-apis-overview.md)
