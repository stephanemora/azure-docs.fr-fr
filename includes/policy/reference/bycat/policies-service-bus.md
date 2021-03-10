---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 9305caa74728217c030b929e2ac0a01d5dca125b
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102429291"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Toutes les règles d’autorisation, sauf RootManageSharedAccessKey, doivent être supprimées de l’espace de noms Service Bus](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1817ec0-a368-432a-8057-8371e17ac6ee) |Les clients Service Bus ne doivent pas utiliser une stratégie d’accès au niveau de l’espace de noms qui donne accès à l’ensemble des files d’attente et rubriques d’un espace de noms. Pour respecter le modèle de sécurité basé sur le privilège minimum, vous devez créer des stratégies d’accès au niveau de l’entité pour les files d’attente et les rubriques afin de limiter l’accès à l’entité spécifique |Audit, Refuser, Désactivé |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditNamespaceAccessRules_Audit.json) |
|[Les journaux de ressources dans Service Bus doivent être activés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Auditez l’activation des journaux de ressources. Permet de recréer les pistes d’activité à utiliser à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau. |AuditIfNotExists, Désactivé |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[Les espaces de noms Service Bus Premium doivent utiliser une clé gérée par le client pour le chiffrement](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F295fc8b1-dc9f-4f53-9c61-3f313ceab40a) |Azure Service Bus prend en charge le chiffrement des données au repos à l’aide de clés gérées par Microsoft (par défaut) ou de clés gérées par le client. Si vous choisissez de chiffrer les données à l’aide de clés gérées par le client, vous pouvez attribuer, faire pivoter, désactiver et révoquer l’accès aux clés utilisées par Service Bus pour chiffrer les données dans votre espace de noms. Notez que Service Bus ne prend en charge que le chiffrement avec des clés gérées par le client pour les espaces de noms premium. |Audit, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_CustomerManagedKeyEnabled_Audit.json) |
