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
ms.openlocfilehash: f6db40a35c289fa0870ac4c9e5e55b773c84f4f4
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679225"
---
# <a name="common-security-attributes-for-azure-service-fabric"></a>Attributs de sécurité courants pour Azure Service Fabric

La sécurité fait partie intégrante d'un service Azure. Cet article décrit les attributs de sécurité courantes intégrées à Azure Service Fabric. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement (côté client, Always Encrypted, etc.)</ul>| Oui | Le client possède le cluster et les machines virtuelles identiques sur lequel le cluster est basé. Chiffrement de disque Azure peut être activé sur les machines virtuelles identiques. |
| Chiffrement en transit :<ul><li>Chiffrement Express Route</li><li>Chiffrement dans le réseau virtuel</li><li>Chiffrement de réseau virtuel à réseau virtuel</ul>| Oui |  |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| Oui | Le client possède le cluster et les machines virtuelles identiques sur lequel le cluster est basé. Chiffrement de disque Azure peut être activé sur les machines virtuelles identiques. |
| Chiffrement au niveau des colonnes (Azure Data Services)| S.O. |  |
| Appels d’API chiffrés| Oui | Les appels d’API Service Fabric sont effectués via Azure Resource Manager. Un jeton web JSON valide (JWT) est nécessaire. |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Oui |  |
| Prise en charge de l’injection de réseau virtuel| Oui |  |
| Prise en charge de l’isolement réseau/l’installation de pare-feu| Oui | Utilisation de groupes de sécurité réseau (NSG). |
| Prise en charge du tunneling forcé | Oui | La gestion réseau Azure fournit le tunneling forcé. |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge (analytique de journal, application insights, etc.) de surveillance Azure| Oui | À l’aide de la prise en charge et la prise en charge par des tiers de surveillance Azure. |

## <a name="iam-support"></a>Prise en charge d’IAM

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Gestion des accès – Authentification| Oui | L’authentification est effectuée via Azure Active Directory. |
| Gestion des accès – Autorisation| Oui | Gestion des identités et des accès (IAM) pour les appels via SFRP. Les appels effectués directement au point de terminaison du cluster prennent en charge deux rôles : utilisateur et administrateur. Le client peut mapper les API sur n’importe lequel des deux rôles. |


## <a name="audit-trail"></a>Piste d’audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion/contrôle| Oui | Toutes les opérations de plan de contrôle sont exécutées par le biais de processus pour l’audit et les approbations. |
| Journalisation et audit du plan de données| S.O. | Le cluster appartient au client.  |

## <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de configuration management (gestion des versions de configuration, etc.).| Oui | La configuration du service est déployée à l’aide du déploiement Azure, qui gère également sa version. La version du code (application et runtime) est gérée à l’aide de la build Azure.