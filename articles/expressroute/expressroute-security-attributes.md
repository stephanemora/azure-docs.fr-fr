---
title: Attributs de sécurité communs pour Azure ExpressRoute
description: Liste de contrôle des attributs de sécurité couramment utilisés pour l’évaluation d’Azure ExpressRoute
services: expressroute
ms.service: expressroute
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: d6156715fb87831d465197fd8eec59d245221e48
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082210"
---
# <a name="common-security-attributes-for-azure-expressroute"></a>Attributs de sécurité communs pour Azure ExpressRoute

La sécurité fait partie intégrante d'un service Azure. Cet article documente les attributs de sécurité courants intégrés dans Azure ExpressRoute.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement (côté client, Always Encrypted, etc.)</ul>|  N/A | ExpressRoute ne stocke pas les données client. |
| Chiffrement en transit :<ul><li>Chiffrement Express Route</li><li>Chiffrement dans le réseau virtuel</li><li>Chiffrement de réseau virtuel à réseau virtuel</ul>| Non | |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| N/A |  |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |
| Appels d’API chiffrés| OUI | Via [Azure Resource Manager](../azure-resource-manager/index.yml) et HTTPS. |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| N/A |  |
| Prise en charge de l’injection de réseau virtuel| N/A | |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI | Chaque client est contenu dans son propre domaine de routage et acheminé vers son propre réseau virtuel |
| Prise en charge du tunneling forcé| N/A | Via le protocole de passerelle frontière (BGP). |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | Consultez [Supervision, métriques et alertes ExpressRoute](expressroute-monitoring-metrics-alerts.md).|

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | Compte de service de passerelle pour Microsoft (GWM) (contrôleur) ; Accès JIT (juste-à-temps) pour le développement et l’exploitation. |
| Authorization|  OUI |Compte de service de passerelle pour Microsoft (GWM) (contrôleur) ; Accès JIT (juste-à-temps) pour le développement et l’exploitation. |


## <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes| 
|---|---|--|
| Journalisation et audit du plan de gestion et de contrôle| OUI |  |
| Journalisation et audit du plan de données| Non |   |

## <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI | Via le fournisseur de ressources réseau (NRP). |
