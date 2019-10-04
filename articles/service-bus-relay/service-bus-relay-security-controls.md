---
title: Contrôles de sécurité pour Azure Service Bus Relay
description: Check-list des contrôles de sécurité utilisés pour l’évaluation d’Azure Service Bus Relay
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 7f4e2a31673905a7e28d1dbb5520650aefc6f368
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219988"
---
# <a name="security-controls-for-azure-service-bus-relay"></a>Contrôles de sécurité pour Azure Service Bus Relay

Cet article décrit les contrôles de sécurité intégrés à Azure Service Bus Relay.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Réseau

| Contrôle de sécurité | Oui/Non | Notes | Documentation |
|---|---|--|--|
| Prise en charge du point de terminaison de service| Non |  |   |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| Non |  |   |
| Prise en charge du tunneling forcé| N/A | Le relais est le tunnel TLS  |   |

## <a name="monitoring--logging"></a>Supervision et journalisation

| Contrôle de sécurité | Oui/Non | Notes| Documentation |
|---|---|--|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | |   |
| Journalisation et audit du plan de gestion et de contrôle| OUI | Via [Azure Resource Manager](../azure-resource-manager/index.yml). |   |
| Journalisation et audit du plan de données| OUI | La réussite, l’échec et les erreurs de la connexion sont journalisés.  |   |

## <a name="identity"></a>Identité

| Contrôle de sécurité | Oui/Non | Notes| Documentation |
|---|---|--|--|
| Authentication| OUI | Via SAS. | [Authentification et autorisation Azure Relay](relay-authentication-and-authorization.md) |
| Authorization|  OUI | Via SAS. | [Authentification et autorisation Azure Relay](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Protection des données

| Contrôle de sécurité | Oui/Non | Notes | Documentation |
|---|---|--|--|
| Chiffrement côté serveur au repos : Clés managées par Microsoft |  N/A | Le relais est un socket web et ne persiste pas dans les données. |   |
| Chiffrement côté serveur au repos : clés gérées par le client (BYOK) | Non | Utilise uniquement les certificats Microsoft TLS.  |   |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |   |
| Chiffrement en transit (comme ExpressRoute, de réseau virtuel et de réseau virtuel à réseau virtuel)| OUI | Le service nécessite TLS. |   |
| Appels d’API chiffrés| OUI | HTTPS. |


## <a name="configuration-management"></a>Gestion des configurations

| Contrôle de sécurité | Oui/Non | Notes| Documentation |
|---|---|--|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI | Via [Azure Resource Manager](../azure-resource-manager/index.yml).|   |

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en plus sur les [contrôles de sécurité intégrés des services Azure](../security/fundamentals/security-controls.md).