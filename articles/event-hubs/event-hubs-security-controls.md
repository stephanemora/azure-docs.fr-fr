---
title: Contrôles de sécurité pour Azure Event Hubs
description: Cet article fournit une liste de vérification des contrôles de sécurité relatifs à l'évaluation d'Azure Event Hubs (réseau, identité, protection des données, etc.).
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: da20778f1e24372e445d635e675df6484905f195
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85315391"
---
# <a name="security-controls-for-azure-event-hubs"></a>Contrôles de sécurité pour Azure Event Hubs

Cet article décrit les contrôles de sécurité intégrés dans Azure Event Hubs.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Réseau

| Contrôle de sécurité | Oui/Non | Notes | Documentation |
|---|---|--|--|
| Prise en charge du point de terminaison de service| Oui |  |  |
| Prise en charge de l’injection de réseau virtuel| Non | |  |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| Oui |  |  |
| Prise en charge du tunneling forcé| Non |  |  |

## <a name="monitoring--logging"></a>Supervision et journalisation

| Contrôle de sécurité | Oui/Non | Notes| Documentation |
|---|---|--|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| Oui | |  |
| Journalisation et audit du plan de gestion et de contrôle| Oui |  |  |
| Journalisation et audit du plan de données| Oui |   |  |

## <a name="identity"></a>Identité

| Contrôle de sécurité | Oui/Non | Notes| Documentation |
|---|---|--|--|
| Authentification| Oui | | [Autoriser l’accès à Azure Event Hubs](authorize-access-event-hubs.md), [Autoriser l’accès aux ressources Event Hubs à l’aide d’Azure Active Directory](authorize-access-azure-active-directory.md), [Autoriser l’accès aux ressources Event Hubs à l’aide de signatures d’accès partagé](authorize-access-shared-access-signature.md) |
| Autorisation|  Oui | | [Authentifier une identité managée avec Azure Active Directory pour accéder aux ressources Event Hubs](authenticate-managed-identity.md), [Authentifier une application avec Azure Active Directory pour accéder aux ressources Event Hubs](authenticate-application.md), [Authentifier l’accès aux ressources Event Hubs à l’aide de signatures d’accès partagé (SAP)](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>Protection de données

| Contrôle de sécurité | Oui/Non | Notes | Documentation |
|---|---|--|--|
| Chiffrement côté serveur au repos : Clés managées par Microsoft |  Oui | |  |
| Chiffrement côté serveur au repos : clés gérées par le client (BYOK) | Oui. Disponible pour les clusters dédiés. | Une clé gérée par le client dans Azure Key Vault peut être utilisée pour chiffrer les données sur un Event Hub au repos. | [Configurer des clés gérées par le client pour chiffrer les données Azure Event Hubs au repos à l’aide du portail Azure](configure-customer-managed-key.md) |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |  |
| Chiffrement en transit (comme ExpressRoute, de réseau virtuel et de réseau virtuel à réseau virtuel)| Oui | |  |
| Appels d’API chiffrés| Oui |  |  |

## <a name="configuration-management"></a>Gestion des configurations

| Contrôle de sécurité | Oui/Non | Notes| Documentation |
|---|---|--|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| Oui | |  |

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en plus sur les [contrôles de sécurité intégrés des services Azure](../security/fundamentals/security-controls.md).
