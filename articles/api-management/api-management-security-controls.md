---
title: Contrôles de sécurité pour Gestion des API Azure
description: Check-list des contrôles de sécurité pour l’évaluation de Gestion des API
services: api-management
author: vladvino
ms.service: api-management
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: vlvinogr
ms.openlocfilehash: 670050efe01fb658fab52a43914f193e9798b828
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751125"
---
# <a name="security-controls-for-api-management"></a>Contrôles de sécurité pour Gestion des API

Cet article décrit les contrôles de sécurité intégrés à Gestion des API.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Réseau

| Contrôle de sécurité | Oui/Non | Notes | Documentation |
|---|---|--|--|
| Prise en charge du point de terminaison de service| Non | |  |
| Prise en charge de l’injection de réseau virtuel| Oui | |  |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| Oui | À l’aide de groupes de sécurité réseau (NSG) et Azure Application Gateway (ou une autre appliance logicielle) respectivement. |  |
| Prise en charge du tunneling forcé| Oui | La gestion réseau Azure fournit le tunneling forcé. |  |

## <a name="monitoring--logging"></a>Supervision et journalisation

| Contrôle de sécurité | Oui/Non | Notes| Documentation |
|---|---|--|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| Oui | | |
| Journalisation et audit du plan de gestion et de contrôle| Oui | [Journaux d’activité Azure Monitor](../azure-monitor/platform/platform-logs-overview.md) | |
| Journalisation et audit du plan de données| Oui | [Journaux de diagnostic Azure Monitor](../azure-monitor/platform/platform-logs-overview.md) et (éventuellement) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  | |


## <a name="identity"></a>Identité

| Contrôle de sécurité | Oui/Non | Notes| Documentation |
|---|---|--|--|
| Authentication| Oui | |  |
| Autorisation| Oui | |  |

## <a name="data-protection"></a>Protection de données

| Contrôle de sécurité | Oui/Non | Notes | Documentation |
|---|---|--|--|
| Chiffrement côté serveur au repos : Clés managées par Microsoft | Oui | Les données sensibles telles que les certificats, les clés et les valeurs secrètes sont chiffrées à l'aide de clés d'instance de service gérées par le service. |  |
| Chiffrement côté serveur au repos : clés gérées par le client (BYOK) | Non | Toutes les clés de chiffrement sont propres à l'instance de service et gérées par ce dernier. |  |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |  |
| Chiffrement en transit (comme ExpressRoute, de réseau virtuel et de réseau virtuel à réseau virtuel)| Oui | Le chiffrement [Express Route](../expressroute/index.yml) et VNet est fourni par la [mise en réseau Azure](../virtual-network/index.yml). |  |
| Appels d’API chiffrés| Oui | Les appels de plan de gestion se font par le biais d'[Azure Resource Manager](../azure-resource-manager/index.yml) sur TLS. Un jeton web JSON valide (JWT) est nécessaire.  Les appels de plan de données peuvent être sécurisés via TLS et un des mécanismes d’authentification pris en charge (certificat client ou JWT, par exemple). |   |
 |

## <a name="configuration-management"></a>Gestion des configurations

| Contrôle de sécurité | Oui/Non | Notes| Documentation |
|---|---|--|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| Oui | À l’aide du [Kit de ressources DevOps Gestion des API Azure](https://aka.ms/apimdevops) |  |

## <a name="vulnerability-scans-false-positives"></a>Faux-positifs des analyses des vulnérabilités

Cette section traite des vulnérabilités courantes qui n’affectent pas Gestion des API Azure.

| Vulnérabilité               | Description                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed constitue une vulnérabilité en matière d'implémentation de l'extension TLS SessionTicket disponible dans certains produits F5. Elle permet la fuite (« bleeding ») d'un maximum de 31 octets de données à partir de la mémoire non initialisée. Ce problème est dû au remplissage de la pile TLS d'un ID de session, transmis par le client, avec des données à hauteur de 32 octets. |

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en plus sur les [contrôles de sécurité intégrés des services Azure](../security/fundamentals/security-controls.md).