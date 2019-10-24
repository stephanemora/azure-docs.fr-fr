---
title: Contrôles de sécurité pour Azure Resource Manager
description: Check-list des contrôles de sécurité intégrés pour l’évaluation du service Azure Resource Manager.
services: azure-resource-manager
author: msmbaldwin
manager: rkarlin
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 78f3f41ca2f0aa45d295fd9846cf3aa96cccea72
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390465"
---
# <a name="security-controls-for-azure-resource-manager"></a>Contrôles de sécurité pour Azure Resource Manager

Cet article décrit les contrôles de sécurité intégrés à Azure Resource Manager.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Protection des données

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement côté serveur au repos : Clés managées par Microsoft | OUI |  |
| Chiffrement en transit (ExpressRoute, de réseau virtuel et de réseau virtuel à réseau virtuel)| OUI | HTTPS/TLS. |
| Chiffrement côté serveur au repos : clés gérées par le client (BYOK) | N/A | Azure Resource Manager ne stocke aucun contenu client, uniquement des données de contrôle. |
| Chiffrement au niveau des colonnes (Azure Data Services)| OUI | |
| Appels d’API chiffrés| OUI | |

## <a name="network"></a>Réseau

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Non | |
| Prise en charge de l’injection de réseau virtuel| OUI | |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| Non |  |
| Prise en charge du tunneling forcé| Non |  |

## <a name="monitoring--logging"></a>Supervision et journalisation

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| Non | |
| Journalisation et audit du plan de gestion et de contrôle| OUI | Les journaux d’activité exposent toutes les opérations d’écriture (PUT, POST, DELETE) effectuées sur vos ressources ; consultez [Afficher les journaux d’activité pour surveiller les actions sur les ressources](resource-group-audit.md). |
| Journalisation et audit du plan de données| N/A | |

## <a name="identity"></a>Identité

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | Basée sur [Azure Active Directory](/azure/active-directory).|
| Authorization| OUI | |

## <a name="configuration-management"></a>Gestion des configurations

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI |  |

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en plus sur les [contrôles de sécurité intégrés des services Azure](../security/fundamentals/security-controls.md).