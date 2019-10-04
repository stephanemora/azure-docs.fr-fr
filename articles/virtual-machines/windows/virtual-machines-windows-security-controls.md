---
title: Contrôles de sécurité pour Machines virtuelles Windows Azure
description: Check-list des contrôles de sécurité pour l’évaluation de Machines virtuelles Windows Azure
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: c4ddda164a2d8a4e2a4d106c2d7bb8bfcbae3690
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886248"
---
# <a name="security-controls-for-windows-virtual-machines"></a>Contrôles de sécurité pour Machines virtuelles Windows

Cet article décrit les contrôles de sécurité intégrés dans Machines virtuelles Windows.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Réseau

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| OUI | |
| Prise en charge de l’injection de réseau virtuel| OUI | |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI |  |
| Prise en charge du tunneling forcé| OUI | Consultez [Configuration du tunneling forcé à l’aide du modèle de déploiement Azure Resource Manager](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

## <a name="monitoring--logging"></a>Supervision et journalisation

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | Consultez [Superviser et mettre à jour une machine virtuelle Linux dans Azure](/azure/virtual-machines/linux/tutorial-monitoring) et [Superviser et mettre à jour une machine virtuelle Windows dans Azure](/azure/virtual-machines/windows/tutorial-monitoring). |
| Journalisation et audit du plan de gestion et de contrôle| OUI |  |
| Journalisation et audit du plan de données | Non |  |

## <a name="identity"></a>Identité

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI |  |
| Authorization| OUI |  |

## <a name="data-protection"></a>Protection des données

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement côté serveur au repos : Clés managées par Microsoft | OUI | Consultez [Comment chiffrer une machine virtuelle Linux dans Azure](/azure/virtual-machines/linux/encrypt-disks) et [Chiffrer des disques virtuels sur une machine virtuelle Windows](/azure/virtual-machines/windows/encrypt-disks). |
| Le chiffrement en transit (tel que le chiffrement ExpressRoute, le chiffrement dans un réseau virtuel, et le chiffrement de réseau virtuel à réseau virtuel)| OUI | Le service Machines virtuelles Azure prend en charge [ExpressRoute](/azure/expressroute) et le chiffrement de réseau virtuel. Consultez [Chiffrement en transit sur des machines virtuelles](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Chiffrement côté serveur au repos : clés gérées par le client (BYOK) | OUI | Les clés gérées par le client sont un scénario de chiffrement Azure pris en charge ; consultez [Vue d’ensemble du chiffrement Azure](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |
| Appels d’API chiffrés| OUI | Par le biais de HTTPS et de SSL. |



## <a name="configuration-management"></a>Gestion des configurations

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI |  | 

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en plus sur les [contrôles de sécurité intégrés des services Azure](../../security/fundamentals/security-controls.md).
