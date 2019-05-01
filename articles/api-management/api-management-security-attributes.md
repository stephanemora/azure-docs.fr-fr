---
title: Attributs de sécurité courantes de gestion des API Azure
description: Une liste de vérification des attributs de sécurité courants pour l’évaluation de gestion des API
services: api-management
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: api-management
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 040d628f9fe89f68a1c5ab91a1522c6a3cb724d9
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "64508139"
---
# <a name="common-security-attributes-for-api-management"></a>Attributs de sécurité courantes de gestion des API

La sécurité fait partie intégrante d'un service Azure. Cet article décrit les attributs de sécurité courantes intégrées à la gestion des API.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement (côté client, Always Encrypted, etc.)</ul>| Oui (chiffrement côté service uniquement) | Données sensibles telles que les certificats, les clés et les valeurs nommées de secret sont chiffrées avec géré par le service, toutes les clés d’instance de service. |
| Chiffrement en transit :<ul><li>Chiffrement Express Route</li><li>Dans le chiffrement du réseau virtuel</li><li>Chiffrement de réseau virtuel à réseau virtuel</ul>| Oui | [Express Route](../expressroute/index.yml) et chiffrement du réseau virtuel est fourni par [mise en réseau Azure](../virtual-network/index.yml). |
| Gestion de clé de chiffrement (clé CMK, BYOK, etc.).| Non  | Toutes les clés de chiffrement sont par instance de service et service managé. |
| Chiffrement au niveau colonne (Services de données Azure)| N/A | |
| Appels d’API chiffrés| Oui | Appels de plan de gestion sont effectuées via [Azure Resource Manager](../azure-resource-manager/index.yml) sur TLS. Un jeton web JSON valide (JWT) est nécessaire.  Appels de plan de données peuvent être sécurisées avec TLS et de mécanismes d’authentification pris en charge (par exemple, le certificat client ou le JWT).
 |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Non  | |
| Prise en charge l’injection de réseau virtuel| Oui | |
| Prise en charge des pare-feu et l’isolement réseau| Oui | À l’aide de groupes de sécurité réseau (NSG) et Azure Application Gateway (ou autre appliance logicielle) respectivement. |
| Prise en charge de tunneling de forcé| Oui | La gestion réseau Azure fournit le tunneling forcé. |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge (analytique de journal, application insights, etc.) de surveillance Azure| Oui | |

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentification| Oui | |
| Authorization| Oui | |


## <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Audit et consignation de plan de contrôle et de gestion| Oui | [Journaux d’activité Azure Monitor](../azure-monitor/platform/activity-logs-overview.md) |
| Audit et consignation de plan de données| Oui | [Journaux de diagnostic Azure Monitor](../azure-monitor/platform/diagnostic-logs-overview.md) et (éventuellement) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  |

## <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de configuration management (gestion des versions de configuration, etc.).| Oui | À l’aide de la [Kit de ressources de DevOps de gestion des API Azure](https://aka.ms/apimdevops) |

## <a name="vulnerability-scans-false-positives"></a>Analyser les vulnérabilités faux positifs

Cette section documente les vulnérabilités courantes, ce qui n’affectent pas la gestion des API Azure.

| Vulnérabilité               | Description                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed est une vulnérabilité dans l’implémentation de l’extension TLS SessionTicket trouvée dans certains produits F5. Il permet la fuite (« bleeding ») de 31 octets de données à partir de la mémoire non initialisée. Cela est dû à la pile TLS remplissage d’un ID de Session passé à partir du client, avec des données pour la rendre 32 bits de long. |