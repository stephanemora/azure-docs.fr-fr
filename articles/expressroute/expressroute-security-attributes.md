---
title: Attributs de sécurité pour Azure ExpressRoute
description: Check-list des attributs de sécurité pour l’évaluation d’Azure ExpressRoute
services: expressroute
ms.service: expressroute
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: c9a46497c18b99ad7774036fd92e63d024b47045
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442203"
---
# <a name="security-attributes-for-azure-expressroute"></a>Attributs de sécurité pour Azure ExpressRoute

Cet article documente les attributs de sécurité intégrés à Azure ExpressRoute.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos (comme le chiffrement côté serveur, le chiffrement côté serveur avec clés managées par le client et d’autres fonctions de chiffrement)|  N/A | ExpressRoute ne stocke pas les données client. |
| Chiffrement en transit (ExpressRoute, de réseau virtuel et de réseau virtuel à réseau virtuel)| Non | |
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
