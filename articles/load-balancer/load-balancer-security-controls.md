---
title: Contrôles de sécurité pour Azure Load Balancer
description: Liste de vérification des contrôles de sécurité pour l’évaluation de Load Balancer
services: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: allensu
ms.openlocfilehash: 6043e574697489b6566641c352bc21a2b6d87f51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74214895"
---
# <a name="security-controls-for-azure-load-balancer"></a>Contrôles de sécurité pour Azure Load Balancer

Cet article documente les contrôles de sécurité intégrés dans Azure Load Balancer.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Réseau

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| N/A | |
| Prise en charge de l’injection de réseau virtuel| N/A | |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| N/A |  |
| Prise en charge du tunneling forcé| N/A | |

## <a name="monitoring--logging"></a>Supervision et journalisation

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| Oui | Consultez les [Journaux Azure Monitor pour une instance publique de Basic Load Balancer](load-balancer-monitor-log.md). |
| Journalisation et audit du plan de gestion et de contrôle| Oui | Consultez les [Journaux Azure Monitor pour une instance publique de Basic Load Balancer](load-balancer-monitor-log.md). |
| Journalisation et audit du plan de données | N/A |  |

## <a name="identity"></a>Identité

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentification| N/A |  |
| Autorisation| N/A |  |

## <a name="data-protection"></a>Protection de données

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement côté serveur au repos : Clés managées par Microsoft | N/A | |
| Le chiffrement en transit (tel que le chiffrement ExpressRoute, le chiffrement dans un réseau virtuel, et le chiffrement de réseau virtuel à réseau virtuel)| N/A | |
| Chiffrement côté serveur au repos : clés gérées par le client (BYOK) | N/A | |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |
| Appels d’API chiffrés| Oui | Via [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="configuration-management"></a>Gestion des configurations

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| N/A |  | 

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en plus sur les [contrôles de sécurité intégrés des services Azure](../security/fundamentals/security-controls.md).