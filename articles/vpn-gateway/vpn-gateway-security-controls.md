---
title: Contrôles de sécurité pour la passerelle VPN Azure
description: Check-list des contrôles de sécurité pour l’évaluation de la passerelle VPN Azure
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 1babb892063da6d460ea2bc4c567da954731956f
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886286"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Contrôles de sécurité pour la passerelle VPN Azure

Cet article décrit les contrôles de sécurité intégrés dans la passerelle VPN Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Réseau

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| N/A | |
| Prise en charge de l’injection de réseau virtuel| N/A | |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI | Les passerelles VPN sont des instances de machine virtuelle dédiées pour chaque réseau virtuel client  |
| Prise en charge du tunneling forcé| OUI |  |

## <a name="monitoring--logging"></a>Supervision et journalisation

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | Consultez [Journaux de diagnostic Azure Monitor/alertes](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Métriques Azure Monitor/alertes](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |
| Journalisation et audit du plan de gestion et de contrôle| OUI | Journal d’activité Azure Resource Manager. |
| Journalisation et audit du plan de données | OUI | [Journaux de diagnostic Azure Monitor](../azure-resource-manager/resource-group-audit.md) pour la journalisation et l’audit de la connectivité VPN. |

## <a name="identity"></a>Identité

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) pour gérer le service et configurer la passerelle VPN Azure. |
| Authorization| OUI | Prise en charge de l'autorisation via [RBAC](../role-based-access-control/overview.md). |

## <a name="data-protection"></a>Protection des données

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement côté serveur au repos : Clés managées par Microsoft | N/A | Données client en transit dans la passerelle VPN, ne stocke PAS les données client |
| Le chiffrement en transit (tel que le chiffrement ExpressRoute, le chiffrement dans un réseau virtuel, et le chiffrement de réseau virtuel à réseau virtuel)| OUI | Les passerelles VPN chiffrent les paquets client entre les passerelles VPN Azure et les périphériques VPN locaux du client (S2S) ou les clients VPN (P2S). Les passerelles VPN prennent également en charge le chiffrement entre réseaux virtuels. |
| Chiffrement côté serveur au repos : clés gérées par le client (BYOK) | Non | Les clés prépartagées spécifiées par le client sont chiffrées au repos, mais pas encore intégrées à CMK. |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |
| Appels d’API chiffrés| OUI | Via [Azure Resource Manager](../azure-resource-manager/index.yml) et HTTPS  |

## <a name="configuration-management"></a>Gestion des configurations

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI | Pour les opérations de gestion, l’état d’une configuration de passerelle VPN Azure peut être exporté sous forme de modèle Azure Resource Manager et géré au fil du temps. | 

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en plus sur les [contrôles de sécurité intégrés des services Azure](../security/fundamentals/security-controls.md).