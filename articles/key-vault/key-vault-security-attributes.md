---
title: Attributs de sécurité courants pour Azure Key Vault
description: Check-list des attributs de sécurité couramment utilisés pour l'évaluation d'Azure Key Vault
services: key-vault
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 1c2265ff5f4c444121bf70c35145703f1b9fe981
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66000194"
---
# <a name="security-attributes-for-azure-key-vault"></a>Attributs de sécurité pour Azure Key Vault

Cet article décrit les attributs de sécurité intégrées à Azure Key Vault. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement (côté client, Always Encrypted, etc.)</ul>| Oui | Tous les objets sont chiffrés. |
| Chiffrement en transit :<ul><li>Chiffrement Express Route</li><li>Dans le chiffrement du réseau virtuel</li><li>Chiffrement de réseau virtuel à réseau virtuel</ul>| Oui | Toutes les communications se font par le biais d’appels d’API chiffrés |
| Gestion de clé de chiffrement (clé CMK, BYOK, etc.).| Oui | Le client contrôle toutes les clés dans l’archivage de clé. Lorsque les clés de module (HSM) soutenu de sécurité matériel sont spécifiées, un module de sécurité 2 de niveau FIPS protège la clé, le certificat ou la clé secrète. |
| Chiffrement au niveau colonne (Services de données Azure)| N/A |  |
| Appels d’API chiffrés| Oui | Utilisation du protocole HTTPS. |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Oui | À l’aide de points de terminaison de service réseau virtuel (VNet). |
| Prise en charge l’injection de réseau virtuel| Non |  |
| Prise en charge des pare-feu et l’isolement réseau| Oui | À l’aide de règles de pare-feu de réseau virtuel. |
| Prise en charge de tunneling de forcé| Non |  |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/non | Notes|
|---|---|--|
| Prise en charge (analytique de journal, application insights, etc.) de surveillance Azure| Oui | Utilisation de Log Analytics. |

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/non | Notes|
|---|---|--|
| Authentication| Oui | L’authentification est effectuée via Azure Active Directory. |
| Authorization| Oui | Utilisation d’une stratégie d’accès à Key Vault. |


## <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion/contrôle| Oui | Utilisation de Log Analytics. |
| Audit et consignation de plan de données| Oui | Utilisation de Log Analytics. |

## <a name="access-controls"></a>Contrôles d'accès

| Attribut de sécurité | Oui/non | Notes|
|---|---|--|
| Contrôles d’accès au plan de gestion/contrôle | Oui | Contrôle d’accès en fonction du rôle (RBAC) Azure Resource Manager |
| Contrôles d’accès au plan de données (à chaque niveau de service) | Oui | Stratégie d’accès à Key Vault |