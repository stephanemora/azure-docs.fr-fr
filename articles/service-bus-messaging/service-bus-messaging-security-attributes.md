---
title: Attributs de sécurité courants pour la messagerie Azure Service Bus
description: Une liste de vérification des attributs de sécurité courants pour l’évaluation de messagerie Azure Service Bus
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: 11977a5aa36b023e468ce6a54862b5138995c417
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "64513516"
---
# <a name="common-security-attributes-for-azure-service-bus-messaging"></a>Attributs de sécurité courants pour la messagerie Azure Service Bus

La sécurité fait partie intégrante d'un service Azure. Cet article décrit les attributs de sécurité courantes intégrées à la messagerie Azure Service Bus.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement (côté client, Always Encrypted, etc.)</ul>|  Oui pour le chiffrement au repos côté serveur par défaut. | Les clés gérées par le client et du BYOK ne sont pas prises en charge encore. Chiffrement côté client est la responsabilité du client |
| Chiffrement en transit :<ul><li>Chiffrement Express Route</li><li>Dans le chiffrement du réseau virtuel</li><li>Chiffrement de réseau virtuel à réseau virtuel</ul>| Oui | Prend en charge du mécanisme HTTPS/TLS standard. |
| Gestion de clé de chiffrement (clé CMK, BYOK, etc.).| Non  |   |
| Chiffrement au niveau colonne (Services de données Azure)| N/A | |
| Appels d’API chiffrés| Oui | Appels d’API sont effectuées via [Azure Resource Manager](../azure-resource-manager/index.yml) et HTTPS. |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Oui (niveau Premium uniquement) | Points de terminaison de service réseau virtuel sont pris en charge pour [niveau de Service Bus Premium](service-bus-premium-messaging.md) uniquement. |
| Prise en charge l’injection de réseau virtuel| Non  | |
| Prise en charge des pare-feu et l’isolement réseau| Oui (niveau Premium uniquement) |  |
| Prise en charge de tunneling de forcé| Non  |  |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge (analytique de journal, application insights, etc.) de surveillance Azure| Oui | Prise en charge par le biais de [Azure Monitor et alertes](service-bus-metrics-azure-monitor.md). |

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentification| Oui | Géré par le biais [Azure Active Directory Managed Service Identity](service-bus-managed-service-identity.md); consultez [authentification et autorisation Service Bus](service-bus-authentication-and-authorization.md).|
| Authorization| Oui | Prend en charge d’autorisation via [RBAC](service-bus-role-based-access-control.md) (version préliminaire) et SAS token ; consultez [authentification et autorisation Service Bus](service-bus-authentication-and-authorization.md). |



## <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Audit et consignation de plan de contrôle et de gestion| Oui | Journaux des opérations sont disponibles ; consultez [les journaux de diagnostic de Service Bus](service-bus-diagnostic-logs.md).  |
| Audit et consignation de plan de données| Non  |  |

## <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de configuration management (gestion des versions de configuration, etc.).| Oui | Prend en charge le contrôle de version du fournisseur de ressources via les [API Azure Resource Manager](/rest/api/resources/).|
