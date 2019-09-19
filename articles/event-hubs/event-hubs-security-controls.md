---
title: Contrôles de sécurité pour Azure Event Hubs
description: Liste de vérification des contrôles de sécurité pour l’évaluation d’Azure Event Hubs
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 3f02315012840b20dc4aa8639ade954f23a5526e
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886323"
---
# <a name="security-controls-for-azure-event-hubs"></a>Contrôles de sécurité pour Azure Event Hubs

Cet article décrit les contrôles de sécurité intégrés dans Azure Event Hubs.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Réseau

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| OUI |  |
| Prise en charge de l’injection de réseau virtuel| Non | |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI |  |
| Prise en charge du tunneling forcé| Non |  |

## <a name="monitoring--logging"></a>Supervision et journalisation

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | |
| Journalisation et audit du plan de gestion et de contrôle| OUI |  |
| Journalisation et audit du plan de données| OUI |   |

## <a name="identity"></a>Identité

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | |
| Authorization|  OUI | |

## <a name="data-protection"></a>Protection des données

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement côté serveur au repos : Clés managées par Microsoft |  OUI | |
| Chiffrement côté serveur au repos : clés gérées par le client (BYOK) | Non |  |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |
| Chiffrement en transit (comme ExpressRoute, de réseau virtuel et de réseau virtuel à réseau virtuel)| OUI | |
| Appels d’API chiffrés| OUI |  |

## <a name="configuration-management"></a>Gestion des configurations

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI | |

## <a name="next-steps"></a>Étapes suivantes

- Découvrez plus d’informations sur les [contrôles de sécurité intégrés des services Azure](../security/fundamentals/security-controls.md).
