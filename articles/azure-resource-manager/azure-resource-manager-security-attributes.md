---
title: Attributs de sécurité courants pour Azure Resource Manager
description: Une liste de vérification des attributs de sécurité courants pour l’évaluation d’Azure Resource Manager
services: azure-resource-manager
author: msmbaldwin
manager: barbkess
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: mbaldwin
ms.openlocfilehash: a771d4c2ae22b7bf149c13c80fe5286ef52a4545
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002257"
---
# <a name="security-attributes-for-azure-resource-manager"></a>Attributs de sécurité pour Azure Resource Manager

Cet article décrit les attributs de sécurité intégrées dans Azure Resource Manager.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement (côté client, Always Encrypted, etc.)</ul>| Oui |  |
| Chiffrement en transit :<ul><li>Chiffrement Express Route</li><li>Dans le chiffrement du réseau virtuel</li><li>Chiffrement de réseau virtuel à réseau virtuel</ul>| Oui | HTTPS/TLS. |
| Gestion de clé de chiffrement (clé CMK, BYOK, etc.).| N/A | Azure Resource Manager ne stocke aucun contenu client, seules les données de contrôle. |
| Chiffrement au niveau colonne (Services de données Azure)| Oui | |
| Appels d’API chiffrés| Oui | |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Non | |
| Prise en charge l’injection de réseau virtuel| Oui | |
| Prise en charge des pare-feu et l’isolement réseau| Non |  |
| Prise en charge de tunneling de forcé| Non |  |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/non | Notes|
|---|---|--|
| Prise en charge (analytique de journal, application insights, etc.) de surveillance Azure| Non | |

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/non | Notes|
|---|---|--|
| Authentication| Oui | [Azure Active Directory](/azure/active-directory) en fonction.|
| Authorization| Oui | |


## <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/non | Notes|
|---|---|--|
| Audit et consignation de plan de contrôle et de gestion| Oui | Journaux d’activité exposent toutes les opérations d’écriture (PUT, POST, DELETE) effectuées sur vos ressources ; consultez [afficher les journaux d’activité pour auditer les actions sur les ressources](resource-group-audit.md). |
| Audit et consignation de plan de données| N/A | |

## <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/non | Notes|
|---|---|--|
| Prise en charge de configuration management (gestion des versions de configuration, etc.).| Oui |  |
