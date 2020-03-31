---
title: Contrôles de sécurité pour Machines virtuelles Linux Azure - Linux
description: Check-list des contrôles de sécurité pour l’évaluation de Machines virtuelles Linux Azure
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 300acaf4a9d2a11ef107e19df99452c909257d54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190572"
---
# <a name="security-controls-for-linux-virtual-machines"></a>Contrôles de sécurité pour Machines virtuelles Linux

Cet article décrit les contrôles de sécurité intégrés dans Machines virtuelles Linux Azure.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Réseau

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Oui | |
| Prise en charge de l’injection de réseau virtuel| Oui | |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| Oui |  |
| Prise en charge du tunneling forcé| Oui | Consultez [Configuration du tunneling forcé à l’aide du modèle de déploiement Azure Resource Manager](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

## <a name="monitoring--logging"></a>Supervision et journalisation

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| Oui | Consultez [Superviser et mettre à jour une machine virtuelle Linux dans Azure](/azure/virtual-machines/linux/tutorial-monitoring). |
| Journalisation et audit du plan de gestion et de contrôle| Oui |  |
| Journalisation et audit du plan de données | Non |  |

## <a name="identity"></a>Identité

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentification| Oui |  |
| Autorisation| Oui |  |

## <a name="data-protection"></a>Protection de données

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement côté serveur au repos : Clés managées par Microsoft | Oui | Consultez [Azure Disk Encryption pour les machines virtuelles Linux](disk-encryption-overview.md). |
| Le chiffrement en transit (tel que le chiffrement ExpressRoute, le chiffrement dans un réseau virtuel, et le chiffrement de réseau virtuel à réseau virtuel)| Oui | Le service Machines virtuelles Azure prend en charge [ExpressRoute](/azure/expressroute) et le chiffrement de réseau virtuel. Consultez [Chiffrement en transit sur des machines virtuelles](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Chiffrement côté serveur au repos : clés gérées par le client (BYOK) | Oui | Les clés gérées par le client sont un scénario de chiffrement Azure pris en charge ; consultez [Vue d’ensemble du chiffrement Azure](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |
| Appels d’API chiffrés| Oui | Par le biais de HTTPS et de TLS. |

## <a name="configuration-management"></a>Gestion des configurations

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| Oui |  | 

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en plus sur les [contrôles de sécurité intégrés des services Azure](../../security/fundamentals/security-controls.md).
