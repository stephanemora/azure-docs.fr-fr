---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/02/2020
ms.author: dacoulte
ms.openlocfilehash: fe88e68ce3266d1a977342c95b08c8456fac1f4c
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262567"
---
|Nom |Description |Effet(s) |Version |GitHub |
|---|---|---|---|---|
|[Toutes les règles d’autorisation, sauf RootManageSharedAccessKey, doivent être supprimées de l’espace de noms Event Hub](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb278e460-7cfc-4451-8294-cccc40a940d7) |Les clients Event Hub ne doivent pas utiliser une stratégie d'accès au niveau de l'espace de noms qui donne accès à l'ensemble des files d'attente et rubriques d'un espace de noms. Pour respecter le modèle de sécurité basé sur le privilège minimum, vous devez créer des stratégies d’accès au niveau de l’entité pour les files d’attente et les rubriques afin de limiter l’accès à l’entité spécifique |Audit, Refuser, Désactivé |1.0.1 |[Lien](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditNamespaceAccessRules_Audit.json)
|[Les règles d’autorisation sur l’instance Event Hub doivent être définies](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4826e5f-6a27-407c-ae3e-9582eb39891d) |Auditer l’existence de règles d’autorisation sur les entités Event Hub pour accorder un accès à privilèges minimum |AuditIfNotExists, Désactivé |1.0.0 |[Lien](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditEventHubAccessRules_Audit.json)
|[Les journaux de diagnostic dans Event Hub doivent être activés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Auditer l’activation des journaux de diagnostic. Permet de recréer les pistes d’activité à utiliser à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau. |AuditIfNotExists, Désactivé |2.0.0 |[Lien](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json)
