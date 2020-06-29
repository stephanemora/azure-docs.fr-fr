---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b28641a184503dc36b5e3f8315e810c69f3be9d3
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2020
ms.locfileid: "84686515"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Localisations Azure Cosmos DB autorisées](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0473574d-2d43-4217-aefe-941fcdf7e684) |Cette stratégie vous permet de limiter les localisations que votre organisation peut spécifier pendant le déploiement de ressources Azure Cosmos DB. Utilisez-la pour appliquer vos exigences de conformité géographique. |[parameters('policyEffect')] |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_Locations_Deny.json) |
|[L’accès en écriture des métadonnées basé sur une clé Azure Cosmos DB doit être désactivé](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4750c32b-89c0-46af-bfcb-2e4541a818d5) |Cette stratégie vous permet de vérifier que tous les comptes Azure Cosmos DB désactivent l’accès en écriture des métadonnées basé sur une clé. |append |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_DisableMetadata_Append.json) |
|[Le débit Azure Cosmos DB doit être limité](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b7ef78e-a035-4f23-b9bd-aff122a1b1cf) |Cette stratégie vous permet de limiter le débit maximal que votre organisation peut spécifier pendant la création de bases de données et de conteneurs Azure Cosmos DB via le fournisseur de ressources. La création de ressources de mise à l’échelle automatique est bloquée. |audit, deny, disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_MaxThroughput_Deny.json) |
|[Cosmos DB doit utiliser un point de terminaison de service de réseau virtuel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Cette stratégie audite Cosmos DB s’il n’est pas configuré pour utiliser un point de terminaison de service de réseau virtuel. |Audit, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Déployer Advanced Threat Protection pour les comptes Cosmos DB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb5f04e03-92a3-4b09-9410-2cc5e5047656) |Cette stratégie active Advanced Threat Protection sur les comptes Cosmos DB. |DeployIfNotExists, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/CosmosDbAdvancedThreatProtection_Deploy.json) |
