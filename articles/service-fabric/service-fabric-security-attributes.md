---
title: Attributs de sécurité pour Azure Service Fabric
description: Check-list des attributs de sécurité utilisés pour l’évaluation d’Azure Service Fabric
services: service-fabric
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: service-fabric
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 23c7f8bdcf67d59ccdd5cd0b00bc0e0960ba1d8f
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443867"
---
# <a name="security-attributes-for-azure-service-fabric"></a>Attributs de sécurité pour Azure Service Fabric

Cet article décrit les attributs de sécurité intégrés à Azure Service Fabric. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos (comme le chiffrement côté serveur, le chiffrement côté serveur avec clés managées par le client et d’autres fonctions de chiffrement)| OUI | Le cluster et le groupe de machines virtuelles identiques sur lequel le cluster est basé appartiennent au client. Le chiffrement de disque Azure peut être activé sur le groupe de machines virtuelles identiques. |
| Chiffrement en transit (ExpressRoute, de réseau virtuel et de réseau virtuel à réseau virtuel)| OUI |  |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| OUI | Le cluster et le groupe de machines virtuelles identiques sur lequel le cluster est basé appartiennent au client. Le chiffrement de disque Azure peut être activé sur le groupe de machines virtuelles identiques. |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A |  |
| Appels d’API chiffrés| OUI | Les appels d’API Service Fabric sont effectués via Azure Resource Manager. Un jeton web JSON valide (JWT) est nécessaire. |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| OUI |  |
| Prise en charge de l’injection de réseau virtuel| OUI |  |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI | Utilisation de groupes de sécurité réseau (NSG). |
| Prise en charge du tunneling forcé| OUI | La gestion réseau Azure fournit le tunneling forcé. |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | Prise en charge de l’utilisation de la surveillance Azure et prise en charge des tiers. |

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | L’authentification est effectuée via Azure Active Directory. |
| Authorization| OUI | Gestion des identités et des accès (IAM) pour les appels via SFRP. Les appels effectués directement au point de terminaison du cluster prennent en charge deux rôles : utilisateur et administrateur. Le client peut mapper les API sur n’importe lequel des deux rôles. |


## <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion et de contrôle| OUI | Toutes les opérations de plan de contrôle sont exécutées par le biais de processus pour l’audit et les approbations. |
| Journalisation et audit du plan de données| N/A | Le cluster appartient au client.  |

## <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI | |