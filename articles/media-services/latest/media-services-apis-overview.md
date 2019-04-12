---
title: Développement avec des API v3 - Azure | Microsoft Docs
description: Cet article décrit les règles qui s’appliquent aux entités et API lors du développement avec Media Services v3.
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
ms.openlocfilehash: 18b72ceaee0ca0747a0bf2144d5f9ffddbee8b8c
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59492139"
---
# <a name="developing-with-media-services-v3-apis"></a>Développement avec Media Services v3 API

Cet article décrit les règles qui s’appliquent aux entités et API lors du développement avec Media Services v3.

## <a name="naming-conventions"></a>Conventions d’affectation de noms

Les noms de ressources Azure Media Services v3 (par exemple Assets, Jobs, Transforms) sont sujets à des restrictions d’appellation par Azure Resource Manager. Conformément à Azure Resource Manager, les noms des ressources sont toujours uniques. Ainsi, vous pouvez utiliser n’importe quelle chaîne d’identificateur unique (par exemple des GUID) pour les noms de vos ressources. 

Les noms de ressources Media Services ne peuvent pas contenir : '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', '.', le caractère de citation unique ou tout caractère de commande. Tous les autres caractères sont autorisés. La longueur maximale d’un nom de ressources est de 260 caractères. 

Pour plus d'informations sur l'affectation de noms dans Azure Resource Manager, consultez : [Exigences en matière d'affectation de noms](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) et [Conventions d'affectation de noms](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="v3-api-design-principles-and-rbac"></a>principes de conception d’API v3 et RBAC

L’un des principes de conception clés de l’API v3 est de renforcer la sécurité de l’API. les API v3 ne retournent pas de secrets ou des informations d’identification sur **obtenir** ou **liste** operations. Les clés sont toujours null, vides ou purgées de la réponse. L’utilisateur doit appeler une méthode d’action séparée pour obtenir des informations d’identification ou des secrets. Le **lecteur** rôle ne peut pas appeler des opérations de sorte qu’il ne peut pas appeler des opérations telles que Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets. Ayant des actions distinctes vous permet de définir des autorisations de sécurité plus granulaires de RBAC dans un rôle personnalisé si vous le souhaitez.

Pour plus d'informations, consultez les pages suivantes :

- [Définitions de rôles intégrés](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)
- [Utilisez RBAC pour gérer l’accès](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Contrôle d’accès en fonction du rôle pour les comptes Media Services](rbac-overview.md)
- [Obtenir une stratégie de clé contenue - .NET](get-content-key-policy-dotnet-howto.md).

## <a name="long-running-operations"></a>Opérations à long terme

Les opérations marquées avec `x-ms-long-running-operation` dans les Services de média Azure [swagger fichiers](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) sont longues opérations en cours d’exécution. 

Pour plus d’informations sur le suivi des opérations asynchrones Azure, consultez [les opérations asynchrones](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Media Services a les opérations de longue suivantes :

* Créer l’événement en direct
* Événement en direct de la mise à jour
* Supprimer l’événement en direct
* Démarrer l’événement en direct
* Arrêter l’événement en direct
* Réinitialisation d’événement en direct
* Créer LiveOutput
* Supprimer LiveOutput
* Créer StreamingEndpoint
* Mise à jour StreamingEndpoint
* Supprimer StreamingEndpoint
* Démarrer StreamingEndpoint
* Arrêter StreamingEndpoint
* Mise à l’échelle StreamingEndpoint

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtrage, classement et pagination d’entités Media Services

Consultez [filtrage, classement, la pagination des entités Azure Media Services](entities-overview.md)

## <a name="next-steps"></a>Étapes suivantes

[Commencer à développer avec l’API Media Services v3 avec des SDK/outils](developers-guide.md)
