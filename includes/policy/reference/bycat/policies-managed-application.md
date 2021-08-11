---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/16/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a49bd6384a85580503bd5a9fc27f8a2986d7e650
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114387978"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[La définition d’application de l’application managée doit utiliser le compte de stockage fourni par le client](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9db7917b-1607-4e7d-a689-bca978dd0633) |Utilisez votre propre compte de stockage pour contrôler les données de définition d’application lorsqu’il s’agit d’une condition de réglementation ou de conformité requise. Vous pouvez choisir de stocker la définition de votre application managée dans un compte de stockage fourni par vos soins lors de la création, de manière à gérer entièrement son emplacement et son accès et répondre ainsi aux conditions de réglementation ou de conformité requises. |audit, deny, disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/ApplicationDefinition_Missing_StorageAccount_Deny.json) |
|[Déployer des associations pour une application managée](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17763ad9-70c0-4794-9397-53d765932634) |Déploie une ressource d’association qui associe les types de ressources sélectionnés à l’application managée spécifiée.  Ce déploiement de stratégie ne prend pas en charge les types de ressources imbriqués. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/AssociationForManagedApplication_Deploy.json) |
