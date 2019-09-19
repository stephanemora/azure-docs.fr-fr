---
title: Contrôles de sécurité pour Azure Service Bus Relay
description: Check-list des contrôles de sécurité utilisés pour l’évaluation d’Azure Service Bus Relay
services: service-bus-relay
ms.service: service-bus-relay
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: a131313f6c270debd9509a934fbf3ce74918ed42
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886299"
---
# <a name="security-controls-for-azure-service-bus-relay"></a>Contrôles de sécurité pour Azure Service Bus Relay

Cet article décrit les contrôles de sécurité intégrés à Azure Service Bus Relay.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Réseau

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Non |  |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| Non |  |
| Prise en charge du tunneling forcé| N/A | Le relais est le tunnel TLS  |

## <a name="monitoring--logging"></a>Supervision et journalisation

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | |
| Journalisation et audit du plan de gestion et de contrôle| OUI | Via [Azure Resource Manager](../azure-resource-manager/index.yml). |
| Journalisation et audit du plan de données| OUI | La réussite, l’échec et les erreurs de la connexion sont journalisés.  |

## <a name="identity"></a>Identité

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | Via SAS. |
| Authorization|  OUI | Via SAS. |

## <a name="data-protection"></a>Protection des données

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement côté serveur au repos : Clés managées par Microsoft |  N/A | Le relais est un socket web et ne persiste pas dans les données. |
| Chiffrement côté serveur au repos : clés gérées par le client (BYOK) | Non | Utilise uniquement les certificats Microsoft TLS.  |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |
| Chiffrement en transit (comme ExpressRoute, de réseau virtuel et de réseau virtuel à réseau virtuel)| OUI | Le service nécessite TLS. |
| Appels d’API chiffrés| OUI | HTTPS. |


## <a name="configuration-management"></a>Gestion des configurations

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI | Via [Azure Resource Manager](../azure-resource-manager/index.yml).|

## <a name="next-steps"></a>Étapes suivantes

- Découvrez plus d’informations sur les [contrôles de sécurité intégrés des services Azure](../security/fundamentals/security-controls.md).