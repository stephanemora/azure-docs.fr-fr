---
title: Définitions de stratégie intégrées pour Azure DDoS Protection Standard
description: Répertorie les définitions de stratégie intégrées Azure Policy pour Azure DDoS Protection Standard. Ces définitions de stratégie intégrées fournissent des approches courantes pour la gestion de vos ressources Azure.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2021
ms.author: yitoh
ms.custom: subject-policy-reference
ms.topic: include
ms.openlocfilehash: 6e62d2a7317b5cf156887811e9428d90af862cf9
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108763574"
---
# <a name="azure-policy-built-in-definitions-for-azure-ddos-protection-standard"></a>Définitions intégrées Azure Policy pour Azure DDoS Protection Standard

Cette page constitue un index des définitions de stratégie intégrées [Azure Policy](../governance/policy/overview.md) pour Azure DDoS Protection Standard. Pour obtenir des éléments intégrés supplémentaires d’Azure Policy pour d’autres services, consultez [Définitions intégrées d’Azure Policy](../governance/policy/samples/built-in-policies.md).

Le nom de chaque définition de stratégie intégrée est un lien vers la définition de la stratégie dans le portail Azure. Utilisez le lien figurant dans la colonne **Version** pour voir la source dans le [dépôt GitHub Azure Policy](https://github.com/Azure/azure-policy).

## <a name="azure-ddos-protection-standard"></a>Service Protection DDos Standard Azure

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les réseaux virtuels doivent être protégés par Azure DDoS Protection Standard](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94de2ad3-e0c1-4caf-ad78-5d47bbc83d3d)|Protégez vos réseaux virtuels contre les attaques volumétriques et de protocole avec Azure DDoS Protection Standard. Pour plus d’informations, consultez [https://aka.ms/ddosprotectiondocs](./ddos-protection-overview.md).|Modifier, Audit, Désactivé|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkDdosStandard_Audit.json)|
|[Les adresses IP publiques doivent avoir les journaux de ressource activés pour Azure DDoS Protection Standard](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F752154a7-1e0f-45c6-a880-ac75a7e4f648)|Activez les journaux de ressources des adresses IP publiques dans les paramètres de diagnostic, afin d’envoyer des données en streaming à un espace de travail Log Analytics. Obtenez une visibilité détaillée du trafic d’attaque et des actions entreprises pour atténuer les attaques DDoS par le biais de notifications, de rapports et de journaux de flux.|AuditIfNotExists, DeployIfNotExists, Désactivé|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/PublicIpDdosLogging_Audit.json)|


## <a name="next-steps"></a>Étapes suivantes

- Consultez les définitions intégrées dans le [dépôt Azure Policy de GitHub](https://github.com/Azure/azure-policy).
- Consultez la [Structure de définition Azure Policy](../governance/policy/concepts/definition-structure.md).
- Consultez la page [Compréhension des effets de Policy](../governance/policy/concepts/effects.md).
