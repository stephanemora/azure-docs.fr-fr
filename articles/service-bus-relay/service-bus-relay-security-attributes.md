---
title: Attributs de sécurité courants pour Azure Service Bus Relay
description: Une liste de vérification des attributs de sécurité courants pour l’évaluation d’Azure Service Bus Relay
services: service-bus-relay
ms.service: service-bus-relay
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: d8ce3c995e8e0f20ed6d694f481cc5fc9fde4fa7
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66000145"
---
# <a name="security-attributes-for-azure-service-bus-relay"></a>Attributs de sécurité pour Azure Service Bus Relay

Cet article décrit les attributs de sécurité intégrées à Azure Service Bus Relay.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement (côté client, Always Encrypted, etc.)</ul>|  N/A | Le relais est un socket web et ne conserve pas les données. |
| Chiffrement en transit :<ul><li>Chiffrement Express Route</li><li>Dans le chiffrement du réseau virtuel</li><li>Chiffrement de réseau virtuel à réseau virtuel</ul>| Oui | Le service nécessite TLS. |
| Gestion de clé de chiffrement (clé CMK, BYOK, etc.).| Non | Utilise uniquement les certificats de Microsoft TLS.  |
| Chiffrement au niveau colonne (Services de données Azure)| N/A | |
| Appels d’API chiffrés| Oui | HTTPS. |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Non |  |
| Prise en charge des pare-feu et l’isolement réseau| Non |  |
| Prise en charge de tunneling de forcé| N/A | Le relais est le tunnel TLS  |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/non | Notes|
|---|---|--|
| Prise en charge (analytique de journal, application insights, etc.) de surveillance Azure| Oui | |

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/non | Notes|
|---|---|--|
| Authentication| Oui | Via SAS. |
| Authorization|  Oui | Via SAS. |


## <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/non | Notes|
|---|---|--|
| Audit et consignation de plan de contrôle et de gestion| Oui | Via [Azure Resource Manager](../azure-resource-manager/index.yml). |
| Audit et consignation de plan de données| Oui | Réussite de la connexion / échec et les erreurs et connecté.  |

## <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/non | Notes|
|---|---|--|
| Prise en charge de configuration management (gestion des versions de configuration, etc.).| Oui | Via [Azure Resource Manager](../azure-resource-manager/index.yml).|
