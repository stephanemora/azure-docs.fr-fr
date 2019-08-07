---
title: Attributs de sécurité pour Gestion des API Azure
description: Check-list des attributs de sécurité pour l’évaluation de la gestion des API
services: api-management
author: msmbaldwin
manager: barbkess
ms.service: api-management
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 2049e2349e3a25ebd4d3f4db19ec47bbaeb067de
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442281"
---
# <a name="security-attributes-for-api-management"></a>Attributs de sécurité pour Gestion des API

Cet article décrit les attributs de sécurité intégrés à Gestion des API.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos (comme le chiffrement côté serveur, le chiffrement côté serveur avec clés managées par le client et d’autres fonctions de chiffrement)| Oui (chiffrement côté service uniquement) | Les données sensibles telles que les certificats, les clés et les valeurs secrètes sont chiffrées à l'aide de clés d'instance de service gérées par le service. |
| Chiffrement en transit (ExpressRoute, de réseau virtuel et de réseau virtuel à réseau virtuel)| OUI | Le chiffrement [Express Route](../expressroute/index.yml) et VNet est fourni par la [mise en réseau Azure](../virtual-network/index.yml). |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| Non | Toutes les clés de chiffrement sont propres à l'instance de service et gérées par ce dernier. |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |
| Appels d’API chiffrés| OUI | Les appels de plan de gestion se font par le biais d'[Azure Resource Manager](../azure-resource-manager/index.yml) sur TLS. Un jeton web JSON valide (JWT) est nécessaire.  Les appels de plan de données peuvent être sécurisés via TLS et un des mécanismes d’authentification pris en charge (certificat client ou JWT, par exemple).
 |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Non | |
| Prise en charge de l’injection de réseau virtuel| OUI | |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI | À l’aide de groupes de sécurité réseau (NSG) et Azure Application Gateway (ou une autre appliance logicielle) respectivement. |
| Prise en charge du tunneling forcé| OUI | La gestion réseau Azure fournit le tunneling forcé. |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | |

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | |
| Authorization| OUI | |


## <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion et de contrôle| OUI | [Journaux d’activité Azure Monitor](../azure-monitor/platform/activity-logs-overview.md) |
| Journalisation et audit du plan de données| OUI | [Journaux de diagnostic Azure Monitor](../azure-monitor/platform/diagnostic-logs-overview.md) et (éventuellement) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  |

## <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI | À l’aide du [Kit de ressources DevOps Gestion des API Azure](https://aka.ms/apimdevops) |

## <a name="vulnerability-scans-false-positives"></a>Faux-positifs des analyses des vulnérabilités

Cette section traite des vulnérabilités courantes, qui n’affectent pas Gestion des API Azure.

| Vulnérabilité               | Description                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed constitue une vulnérabilité en matière d'implémentation de l'extension TLS SessionTicket disponible dans certains produits F5. Elle permet la fuite (« bleeding ») d'un maximum de 31 octets de données à partir de la mémoire non initialisée. Ce problème est dû au remplissage de la pile TLS d'un ID de session, transmis par le client, avec des données à hauteur de 32 octets. |
