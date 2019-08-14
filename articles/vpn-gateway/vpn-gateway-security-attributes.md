---
title: Attributs de sécurité pour la passerelle VPN Azure
description: Liste de contrôle des attributs de sécurité pour l’évaluation de la passerelle VPN Azure
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: c355f70975f441609304a4c9ee2ead75f0e0ce25
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444598"
---
# <a name="security-attributes-for-azure-vpn-gateway"></a>Attributs de sécurité pour la passerelle VPN Azure

Cet article documente les attributs de sécurité intégrés dans la passerelle VPN Azure.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos (comme le chiffrement côté serveur, le chiffrement côté serveur avec clés managées par le client et d’autres fonctions de chiffrement) | N/A | Données client en transit dans la passerelle VPN, ne stocke PAS les données client |
| Le chiffrement en transit (tel que le chiffrement ExpressRoute, le chiffrement dans un réseau virtuel, et le chiffrement de réseau virtuel à réseau virtuel)| OUI | Les passerelles VPN chiffrent les paquets client entre les passerelles VPN Azure et les périphériques VPN locaux du client (S2S) ou les clients VPN (P2S). Les passerelles VPN prennent également en charge le chiffrement entre réseaux virtuels. |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| Non | Les clés prépartagées spécifiées par le client sont chiffrées au repos, mais pas encore intégrées à CMK. |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |
| Appels d’API chiffrés| OUI | Via [Azure Resource Manager](../azure-resource-manager/index.yml) et HTTPS  |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| N/A | |
| Prise en charge de l’injection de réseau virtuel| N/A | . |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI | Les passerelles VPN sont des instances de machine virtuelle dédiées pour chaque réseau virtuel client  |
| Prise en charge du tunneling forcé| OUI |  |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | Consultez [Journaux de diagnostic Azure Monitor/alertes](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Métriques Azure Monitor/alertes](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) pour gérer le service et configurer la passerelle VPN Azure. |
| Authorization| OUI | Prise en charge de l'autorisation via [RBAC](../role-based-access-control/overview.md). |


## <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion et de contrôle| OUI | Journal d’activité Azure Resource Manager. |
| Journalisation et audit du plan de données | OUI | [Journaux de diagnostic Azure Monitor](../azure-resource-manager/resource-group-audit.md) pour la journalisation et l’audit de la connectivité VPN. |

## <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI | Pour les opérations de gestion, l’état d’une configuration de passerelle VPN Azure peut être exporté sous forme de modèle Azure Resource Manager et géré au fil du temps. | 