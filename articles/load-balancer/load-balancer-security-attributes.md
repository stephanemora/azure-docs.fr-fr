---
title: Attributs de sécurité pour Azure Load Balancer
description: Liste de contrôle des attributs de sécurité pour l’évaluation de Load Balancer
services: load-balancer
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbaldwin
ms.openlocfilehash: 7d40618d5f4fde4a2b3fdfbde8a6de0a049ce3b6
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68440866"
---
# <a name="security-attributes-for-azure-load-balancer"></a>Attributs de sécurité pour Azure Load Balancer

Cet article documente les attributs de sécurité intégrés dans Azure Load Balancer.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos (comme le chiffrement côté serveur, le chiffrement côté serveur avec clés managées par le client et d’autres fonctions de chiffrement) | N/A | |
| Le chiffrement en transit (tel que le chiffrement ExpressRoute, le chiffrement dans un réseau virtuel, et le chiffrement de réseau virtuel à réseau virtuel)| N/A | |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| N/A | |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |
| Appels d’API chiffrés| OUI | Via [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| N/A | |
| Prise en charge de l’injection de réseau virtuel| N/A | . |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| N/A |  |
| Prise en charge du tunneling forcé| N/A | |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | Consultez les [Journaux Azure Monitor pour une instance publique de Basic Load Balancer](load-balancer-monitor-log.md). |

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| N/A |  |
| Authorization| N/A |  |


## <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion et de contrôle| OUI | Consultez les [Journaux Azure Monitor pour une instance publique de Basic Load Balancer](load-balancer-monitor-log.md). |
| Journalisation et audit du plan de données | N/A |  |

## <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| N/A |  | 
