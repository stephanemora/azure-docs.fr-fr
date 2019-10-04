---
title: Contrôles de sécurité pour Azure Event Hubs
description: Liste de vérification des contrôles de sécurité pour l’évaluation d’Azure Event Hubs
services: event-hubs
ms.service: event-hubs
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 277a745d26961ed509258d5423fc3c0da9b79a24
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219397"
---
# <a name="security-controls-for-azure-event-hubs"></a>Contrôles de sécurité pour Azure Event Hubs

Cet article décrit les contrôles de sécurité intégrés dans Azure Event Hubs.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Réseau

| Contrôle de sécurité | Oui/Non | Notes | Documentation |
|---|---|--|--|
| Prise en charge du point de terminaison de service| OUI |  |  |
| Prise en charge de l’injection de réseau virtuel| Non | |  |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI |  |  |
| Prise en charge du tunneling forcé| Non |  |  |

## <a name="monitoring--logging"></a>Supervision et journalisation

| Contrôle de sécurité | Oui/Non | Notes| Documentation |
|---|---|--|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | |  |
| Journalisation et audit du plan de gestion et de contrôle| OUI |  |  |
| Journalisation et audit du plan de données| OUI |   |  |

## <a name="identity"></a>Identité

| Contrôle de sécurité | Oui/Non | Notes| Documentation |
|---|---|--|--|
| Authentication| OUI | | [Autoriser l’accès à Azure Event Hubs](authorize-access-event-hubs.md), [Autoriser l’accès aux ressources Event Hubs à l’aide d’Azure Active Directory](authorize-access-azure-active-directory.md), [Autoriser l’accès aux ressources Event Hubs à l’aide de signatures d’accès partagé](authorize-access-shared-access-signature.md) |
| Authorization|  OUI | | [Authentifier une identité managée avec Azure Active Directory pour accéder aux ressources Event Hubs](authenticate-managed-identity.md), [Authentifier une application avec Azure Active Directory pour accéder aux ressources Event Hubs](authenticate-application.md), [Authentifier l’accès aux ressources Event Hubs à l’aide de signatures d’accès partagé (SAP)](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>Protection des données

| Contrôle de sécurité | Oui/Non | Notes | Documentation |
|---|---|--|--|
| Chiffrement côté serveur au repos : Clés managées par Microsoft |  OUI | |  |
| Chiffrement côté serveur au repos : clés gérées par le client (BYOK) | Non |  |  |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |  |
| Chiffrement en transit (comme ExpressRoute, de réseau virtuel et de réseau virtuel à réseau virtuel)| OUI | |  |
| Appels d’API chiffrés| OUI |  |  |

## <a name="configuration-management"></a>Gestion des configurations

| Contrôle de sécurité | Oui/Non | Notes| Documentation |
|---|---|--|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI | |  |

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en plus sur les [contrôles de sécurité intégrés des services Azure](../security/fundamentals/security-controls.md).
