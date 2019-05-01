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
ms.openlocfilehash: f8827ac290393c9f394c3b13149555a1a2aa6df9
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927491"
---
# <a name="common-security-attributes-for-azure-service-bus-relay"></a>Attributs de sécurité courants pour Azure Service Bus Relay

La sécurité fait partie intégrante d'un service Azure. Cet article décrit les attributs de sécurité courantes intégrées à Azure Service Bus Relay.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement (côté client, Always Encrypted, etc.)</ul>|  N/A | Le relais est un socket web et ne conserve pas les données. |
| Chiffrement en transit :<ul><li>Chiffrement Express Route</li><li>Chiffrement dans le réseau virtuel</li><li>Chiffrement de réseau virtuel à réseau virtuel</ul>| Oui | Le service nécessite TLS. |
| Gestion de clé de chiffrement (clé CMK, BYOK, etc.).| Non  | Utilise uniquement les certificats de Microsoft TLS.  |
| Chiffrement au niveau colonne (Services de données Azure)| N/A | |
| Appels d’API chiffrés| Oui | HTTPS. |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Non  |  |
| Prise en charge des pare-feu et l’isolement réseau| Non  |  |
| Prise en charge de tunneling de forcé| N/A | Le relais est le tunnel TLS  |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge (analytique de journal, application insights, etc.) de surveillance Azure| Oui | |

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentification| Oui | Via SAS. |
| Authorization|  Oui | Via SAS. |


## <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Audit et consignation de plan de contrôle et de gestion| Oui | Via [Azure Resource Manager](../azure-resource-manager/index.yml). |
| Audit et consignation de plan de données| Oui | Réussite de la connexion / échec et les erreurs et connecté.  |

## <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de configuration management (gestion des versions de configuration, etc.).| Oui | Via [Azure Resource Manager](../azure-resource-manager/index.yml).|
