---
title: Contrôles de sécurité pour la messagerie Azure Service Bus
description: Check-list des contrôles de sécurité utilisés pour l’évaluation de la messagerie Azure Service Bus
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 3130150a227076befae3f58f65e00a36578b68d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85341630"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Contrôles de sécurité pour la messagerie Azure Service Bus

Cet article décrit les contrôles de sécurité intégrés à la messagerie Azure Service Bus.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Réseau

| Contrôle de sécurité | Oui/Non | Notes | Documentation |
|---|---|--|--|
| Prise en charge du point de terminaison de service| Oui (niveau Premium uniquement) | Les points de terminaison de service de réseau virtuel sont uniquement pris en charge pour le [niveau Service Bus Premium](service-bus-premium-messaging.md). |  |
| Prise en charge de l’injection de réseau virtuel| Non | |  |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| Oui (niveau Premium uniquement) |  |  |
| Prise en charge du tunneling forcé| Non |  |  |

## <a name="monitoring--logging"></a>Supervision et journalisation

| Contrôle de sécurité | Oui/Non | Notes| Documentation |
|---|---|--|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| Oui | Prise en charge par le biais de [Azure Monitor and Alerts](service-bus-metrics-azure-monitor.md). |  |
| Journalisation et audit du plan de gestion et de contrôle| Oui | Des journaux d’opérations sont disponibles.  | [Journaux de diagnostic Service Bus](service-bus-diagnostic-logs.md) |
| Journalisation et audit du plan de données| Non |  |

## <a name="identity"></a>Identité

| Contrôle de sécurité | Oui/Non | Notes| Documentation |
|---|---|--|--|
| Authentification| Oui | Géré avec la [fonctionnalité Managed Service Identity d’Azure Active Directory](service-bus-managed-service-identity.md).| [Authentification et autorisation Service Bus](service-bus-authentication-and-authorization.md) |
| Autorisation| Oui | Prend en charge l’autorisation par [contrôle d’accès en fonction du rôle (RBAC)](authenticate-application.md) et jeton SAS. | [Authentification et autorisation Service Bus](service-bus-authentication-and-authorization.md) |

## <a name="data-protection"></a>Protection de données

| Contrôle de sécurité | Oui/Non | Notes | Documentation |
|---|---|--|--|
| Chiffrement côté serveur au repos : Clés managées par Microsoft |  Oui pour le chiffrement côté serveur au repos par défaut. |  |  |
| Chiffrement côté serveur au repos : clés gérées par le client (BYOK) | Oui. | Une clé gérée par le client dans Azure KeyVault peut être utilisée pour chiffrer les données sur l’espace de noms Service Bus au repos. | [Configurer des clés gérées par le client pour chiffrer les données Azure Service Bus au repos à l’aide du portail Azure](configure-customer-managed-key.md)  |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |   |
| Chiffrement en transit (comme ExpressRoute, de réseau virtuel et de réseau virtuel à réseau virtuel)| Oui | Prend en charge le mécanisme HTTPS/TLS standard. |   |
| Appels d’API chiffrés| Oui | Les appels d’API sont effectués via [Azure Resource Manager](../azure-resource-manager/index.yml) et HTTPS. |   |

## <a name="configuration-management"></a>Gestion des configurations

| Contrôle de sécurité | Oui/Non | Notes| Documentation |
|---|---|--|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| Oui | Prend en charge le contrôle de version du fournisseur de ressources via l’[API Azure Resource Manager](/rest/api/resources/).|   |

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en plus sur les [contrôles de sécurité intégrés des services Azure](../security/fundamentals/security-controls.md).
