---
title: Attributs de sécurité communs pour la messagerie Azure Service Bus
description: Check-list des attributs de sécurité couramment utilisés pour l’évaluation de la messagerie Azure Service Bus
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: d68ffe6561da6a23c288dfabd1d3eb6b34099bb3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66003107"
---
# <a name="security-attributes-for-azure-service-bus-messaging"></a>Attributs de sécurité pour la messagerie Azure Service Bus

Cet article décrit les attributs de sécurité intégrés à la messagerie Azure Service Bus.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement (côté client, Always Encrypted, etc.)</ul>|  Oui pour le chiffrement côté serveur au repos par défaut. | Les clés gérées par le client et du BYOK ne sont pas encore prises en charge. Le chiffrement côté client est sous la responsabilité du client |
| Chiffrement en transit :<ul><li>Chiffrement Express Route</li><li>Chiffrement dans le réseau virtuel</li><li>Chiffrement de réseau virtuel à réseau virtuel</ul>| OUI | Prend en charge le mécanisme HTTPS/TLS standard. |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| Non |   |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |
| Appels d’API chiffrés| OUI | Les appels d’API sont effectués via [Azure Resource Manager](../azure-resource-manager/index.yml) et HTTPS. |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Oui (niveau Premium uniquement) | Les points de terminaison de service de réseau virtuel sont uniquement pris en charge pour le [niveau Service Bus Premium](service-bus-premium-messaging.md). |
| Prise en charge de l’injection de réseau virtuel| Non | |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| Oui (niveau Premium uniquement) |  |
| Prise en charge du tunneling forcé| Non |  |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | Prise en charge par le biais de [Azure Monitor and Alerts](service-bus-metrics-azure-monitor.md). |

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | Géré par le biais de [l’identité de service managé Azure Active Directory ](service-bus-managed-service-identity.md); consultez [Authentification et autorisation Service Bus](service-bus-authentication-and-authorization.md).|
| Authorization| OUI | Prend en charge l’autorisation via [RBAC](service-bus-role-based-access-control.md) (préversion) et jeton SAS ; consultez [Authentification et autorisation Service Bus](service-bus-authentication-and-authorization.md). |



## <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion et de contrôle| OUI | Les journaux des opérations sont disponibles ; consultez [Journaux de diagnostic Service Bus](service-bus-diagnostic-logs.md).  |
| Journalisation et audit du plan de données| Non |  |

## <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI | Prend en charge le contrôle de version du fournisseur de ressources via l’[API Azure Resource Manager](/rest/api/resources/).|
