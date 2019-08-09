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
ms.openlocfilehash: 0e58e7b3f77d9bb673e300aa60ad07ca9dba5153
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082166"
---
# <a name="security-attributes-for-azure-vpn-gateway"></a>Attributs de sécurité pour la passerelle VPN Azure

Cet article documente les attributs de sécurité courants intégrés dans la passerelle VPN Azure.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos (comme le chiffrement côté serveur, le chiffrement côté serveur avec clés managées par le client et d’autres fonctions de chiffrement) | N/A | La passerelle VPN fait transiter les données client, elle ne les stocke pas |
| Le chiffrement en transit (tel que le chiffrement ExpressRoute, le chiffrement dans un réseau virtuel, et le chiffrement de réseau virtuel à réseau virtuel)| OUI | La passerelle VPN chiffre les paquets clients qui transitent entre les passerelles VPN Azure et les appareils VPN locaux (S2S) ou les clients VPN (P2S). Les passerelles VPN prennent également en charge le chiffrement réseau virtuel à réseau virtuel. |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| Non | Les clés prépartagées spécifiées par le client sont chiffrées au repos, mais elles ne sont pas encore intégrées aux clés CMK. |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |
| Appels d’API chiffrés| OUI | Via [Azure Resource Manager](../azure-resource-manager/index.yml) et HTTPS  |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| N/A | |
| Prise en charge de l’injection de réseau virtuel| N/A | . |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI | Les passerelles VPN sont des instances de machine virtuelle dédiées à chaque réseau virtuel client  |
| Prise en charge du tunneling forcé| OUI |  |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | Voir [Journaux de diagnostic Azure Monitor/Alerte](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Métriques Azure Monitor/Alerte](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) pour la gestion du service et la configuration de la passerelle VPN Azure. |
| Authorization| OUI | Prise en charge des autorisations via [RBAC](../role-based-access-control/overview.md). |


## <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion et de contrôle| OUI | Journal d’activité Azure Resource Manager. |
| Journalisation et audit du plan de données | OUI | [Journaux de diagnostic Azure Monitor](../azure-resource-manager/resource-group-audit.md) pour la journalisation et l’audit de la connectivité VPN. |

## <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI | Pour les opérations de gestion, l’état d’une configuration de passerelle VPN Azure peut être exporté sous forme de modèle Azure Resource Manager et géré au fil du temps. | 