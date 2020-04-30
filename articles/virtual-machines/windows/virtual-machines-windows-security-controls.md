---
title: Contrôles de sécurité pour Machines virtuelles Windows Azure
description: Check-list des contrôles de sécurité pour l’évaluation de Machines virtuelles Windows Azure
ms.service: virtual-machines
ms.subservice: security
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: ac1ed9ac25d65d0391175fc6d43b48048da74926
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82101584"
---
# <a name="security-controls-for-windows-virtual-machines"></a>Contrôles de sécurité pour Machines virtuelles Windows

Cet article décrit les contrôles de sécurité intégrés dans Machines virtuelles Windows.

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
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| Oui | [Superviser et mettre à jour une machine virtuelle Windows dans Azure](tutorial-monitoring.md). |
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
| Chiffrement côté serveur au repos : Clés managées par Microsoft | Oui | Consultez [Chiffrer des disques virtuels sur une machine virtuelle Windows](/azure/virtual-machines/windows/disk-encryption-overview). |
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
