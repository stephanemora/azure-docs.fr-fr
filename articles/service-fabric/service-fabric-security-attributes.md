---
title: Attributs de sécurité courants pour Azure Service Fabric
description: Check-list des attributs de sécurité couramment utilisés pour l'évaluation d'Azure Service Fabric
services: service-fabric
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: service-fabric
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 7c1718298c3f7c3fea28fa0b18569085f071696f
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003052"
---
# <a name="security-attributes-for-azure-service-fabric"></a>Attributs de sécurité pour Azure Service Fabric

Cet article décrit les attributs de sécurité intégrés à Azure Service Fabric. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement (côté client, Always Encrypted, etc.)</ul>| Oui | Le client possède le cluster et les machines virtuelles identiques sur lequel le cluster est basé. Chiffrement de disque Azure peut être activé sur les machines virtuelles identiques. |
| Chiffrement en transit :<ul><li>Chiffrement Express Route</li><li>Dans le chiffrement du réseau virtuel</li><li>Chiffrement de réseau virtuel à réseau virtuel</ul>| Oui |  |
| Gestion de clé de chiffrement (clé CMK, BYOK, etc.).| Oui | Le client possède le cluster et les machines virtuelles identiques sur lequel le cluster est basé. Chiffrement de disque Azure peut être activé sur les machines virtuelles identiques. |
| Chiffrement au niveau colonne (Services de données Azure)| N/A |  |
| Appels d’API chiffrés| Oui | Les appels d’API Service Fabric sont effectués via Azure Resource Manager. Un jeton web JSON valide (JWT) est nécessaire. |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Oui |  |
| Prise en charge l’injection de réseau virtuel| Oui |  |
| Prise en charge des pare-feu et l’isolement réseau| Oui | Utilisation de groupes de sécurité réseau (NSG). |
| Prise en charge de tunneling de forcé| Oui | La gestion réseau Azure fournit le tunneling forcé. |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/non | Notes|
|---|---|--|
| Prise en charge (analytique de journal, application insights, etc.) de surveillance Azure| Oui | À l’aide de la prise en charge et la prise en charge par des tiers de surveillance Azure. |

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/non | Notes|
|---|---|--|
| Authentication| Oui | L’authentification est effectuée via Azure Active Directory. |
| Authorization| Oui | Gestion des identités et des accès (IAM) pour les appels via SFRP. Les appels effectués directement au point de terminaison du cluster prennent en charge deux rôles : utilisateur et administrateur. Le client peut mapper les API sur n’importe lequel des deux rôles. |


## <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/non | Notes|
|---|---|--|
| Audit et consignation de plan de contrôle et de gestion| Oui | Toutes les opérations de plan de contrôle sont exécutées par le biais de processus pour l’audit et les approbations. |
| Audit et consignation de plan de données| N/A | Le cluster appartient au client.  |

## <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/non | Notes|
|---|---|--|
| Prise en charge de configuration management (gestion des versions de configuration, etc.).| Oui | |