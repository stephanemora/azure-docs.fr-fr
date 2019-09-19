---
title: Contrôles de sécurité pour Azure Service Fabric
description: Check-list des contrôles de sécurité pour évaluer Azure Service Fabric
services: service-fabric
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.service: service-fabric
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: d62c7848588c494c8190f0d429ce2d6641928b52
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886192"
---
# <a name="security-controls-for-azure-service-fabric"></a>Contrôles de sécurité pour Azure Service Fabric

Cet article décrit les contrôles de sécurité intégrés à Azure Service Fabric. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Réseau

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| OUI |  |
| Prise en charge de l’injection de réseau virtuel| OUI |  |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI | Utilisation de groupes de sécurité réseau (NSG). |
| Prise en charge du tunneling forcé| OUI | La gestion réseau Azure fournit le tunneling forcé. |

## <a name="monitoring--logging"></a>Supervision et journalisation

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | Prise en charge de l’utilisation de la surveillance Azure et prise en charge des tiers. |
| Journalisation et audit du plan de gestion et de contrôle| OUI | Toutes les opérations de plan de contrôle sont exécutées par le biais de processus pour l’audit et les approbations. |
| Journalisation et audit du plan de données| N/A | Le cluster appartient au client.  |

## <a name="identity"></a>Identité

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | L’authentification est effectuée via Azure Active Directory. |
| Authorization| OUI | Gestion des identités et des accès (IAM) pour les appels via SFRP. Les appels effectués directement au point de terminaison du cluster prennent en charge deux rôles : utilisateur et administrateur. Le client peut mapper les API sur n’importe lequel des deux rôles. |

## <a name="data-protection"></a>Protection des données

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement côté serveur au repos : Clés managées par Microsoft | OUI | Le cluster et le groupe de machines virtuelles identiques sur lequel le cluster est basé appartiennent au client. Le chiffrement de disque Azure peut être activé sur le groupe de machines virtuelles identiques. |
| Chiffrement côté serveur au repos : clés gérées par le client (BYOK) | OUI | Le cluster et le groupe de machines virtuelles identiques sur lequel le cluster est basé appartiennent au client. Le chiffrement de disque Azure peut être activé sur le groupe de machines virtuelles identiques. |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A |  |
| Chiffrement en transit (comme ExpressRoute, de réseau virtuel et de réseau virtuel à réseau virtuel)| OUI |  |
| Appels d’API chiffrés| OUI | Les appels d’API Service Fabric sont effectués via Azure Resource Manager. Un jeton web JSON valide (JWT) est nécessaire. |

## <a name="configuration-management"></a>Gestion des configurations

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI | |

## <a name="next-steps"></a>Étapes suivantes

- Découvrez-en plus sur les [contrôles de sécurité intégrés aux services Azure](../security/fundamentals/security-controls.md).