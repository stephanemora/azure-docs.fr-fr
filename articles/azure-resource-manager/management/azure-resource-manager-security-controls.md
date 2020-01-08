---
title: Contrôles de sécurité
description: Check-list des contrôles de sécurité intégrés pour l’évaluation du service Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: d0a0625153e428a0d261e52d40b31ef5142eddfd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75476719"
---
# <a name="security-controls-for-azure-resource-manager"></a>Contrôles de sécurité pour Azure Resource Manager

Cet article décrit les contrôles de sécurité intégrés à Azure Resource Manager.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Protection de données

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement côté serveur au repos : Clés managées par Microsoft | Oui |  |
| Chiffrement en transit (comme ExpressRoute, de réseau virtuel et de réseau virtuel à réseau virtuel)| Oui | HTTPS/TLS. |
| Chiffrement côté serveur au repos : clés gérées par le client (BYOK) | N/A | Azure Resource Manager ne stocke aucun contenu client, uniquement des données de contrôle. |
| Chiffrement au niveau des colonnes (Azure Data Services)| Oui | |
| Appels d’API chiffrés| Oui | |

## <a name="network"></a>Réseau

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Non | |
| Prise en charge de l’injection de réseau virtuel| Oui | |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| Non |  |
| Prise en charge du tunneling forcé| Non |  |

## <a name="monitoring--logging"></a>Supervision et journalisation

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| Non | |
| Journalisation et audit du plan de gestion et de contrôle| Oui | Les journaux d’activité exposent toutes les opérations d’écriture (PUT, POST, DELETE) effectuées sur vos ressources ; consultez [Afficher les journaux d’activité pour surveiller les actions sur les ressources](view-activity-logs.md). |
| Journalisation et audit du plan de données| N/A | |

## <a name="identity"></a>Identité

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| Oui | Basée sur [Azure Active Directory](/azure/active-directory).|
| Autorisation| Oui | |

## <a name="configuration-management"></a>Gestion des configurations

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| Oui |  |

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en plus sur les [contrôles de sécurité intégrés des services Azure](../../security/fundamentals/security-controls.md).