---
title: Attributs de sécurité pour Azure Service Bus Relay
description: Check-list des attributs de sécurité utilisés pour l’évaluation d’Azure Service Bus Relay
services: service-bus-relay
ms.service: service-bus-relay
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: 2047f64914d4a286e6de38b7b2c8524d98eba562
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443879"
---
# <a name="security-attributes-for-azure-service-bus-relay"></a>Attributs de sécurité pour Azure Service Bus Relay

Cet article décrit les attributs de sécurité intégrés à Azure Service Bus Relay.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos (comme le chiffrement côté serveur, le chiffrement côté serveur avec clés managées par le client et d’autres fonctions de chiffrement)|  N/A | Le relais est un socket web et ne persiste pas dans les données. |
| Chiffrement en transit (ExpressRoute, de réseau virtuel et de réseau virtuel à réseau virtuel)| OUI | Le service nécessite TLS. |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| Non | Utilise uniquement les certificats Microsoft TLS.  |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |
| Appels d’API chiffrés| OUI | HTTPS. |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Non |  |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| Non |  |
| Prise en charge du tunneling forcé| N/A | Le relais est le tunnel TLS  |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | |

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | Via SAS. |
| Authorization|  OUI | Via SAS. |


## <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion et de contrôle| OUI | Via [Azure Resource Manager](../azure-resource-manager/index.yml). |
| Journalisation et audit du plan de données| OUI | La réussite, l’échec et les erreurs de la connexion sont journalisés.  |

## <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI | Via [Azure Resource Manager](../azure-resource-manager/index.yml).|
