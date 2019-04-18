---
title: Attributs de sécurité courants pour Azure Key Vault
description: Check-list des attributs de sécurité couramment utilisés pour l'évaluation d'Azure Key Vault
services: key-vault
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: fa36a3c1eb6bda109c7985fa7cade496d2ccf9f4
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59677797"
---
# <a name="common-security-attributes-for-azure-key-vault"></a>Attributs de sécurité courants pour Azure Key Vault

La sécurité fait partie intégrante d'un service Azure. Cet article décrit les attributs de sécurité courantes intégrées à Azure Key Vault. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement (côté client, Always Encrypted, etc.)</ul>| Oui | Tous les objets sont chiffrés. |
| Chiffrement en transit :<ul><li>Chiffrement Express Route</li><li>Chiffrement dans le réseau virtuel</li><li>Chiffrement de réseau virtuel à réseau virtuel</ul>| Oui | Toutes les communications se font par le biais d’appels d’API chiffrés |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| Oui | Le client contrôle toutes les clés dans l’archivage de clé. Lorsque les clés de module (HSM) soutenu de sécurité matériel sont spécifiées, un module de sécurité 2 de niveau FIPS protège la clé, le certificat ou la clé secrète. |
| Chiffrement au niveau des colonnes (Azure Data Services)| S.O. |  |
| Appels d’API chiffrés| Oui | Utilisation du protocole HTTPS. |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Oui | Utilisation de points de terminaison de service de réseau virtuel. |
| Prise en charge de l’injection de réseau virtuel| Non  |  |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| Oui | Utilisation de règles de pare-feu de réseau virtuel. |
| Prise en charge du tunneling forcé | Non  |  |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge (analytique de journal, application insights, etc.) de surveillance Azure| Oui | Utilisation de Log Analytics. |

## <a name="iam-support"></a>Prise en charge de la gestion des identités et des accès (IAM)

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Gestion des accès – Authentification| Oui | L’authentification est effectuée via Azure Active Directory. |
| Gestion des accès – Autorisation| Oui | Utilisation d’une stratégie d’accès à Key Vault. |


## <a name="audit-trail"></a>Piste d’audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion/contrôle| Oui | Utilisation de Log Analytics. |
| Journalisation et audit du plan de données| Oui | Utilisation de Log Analytics. |

## <a name="access-controls"></a>Contrôles d’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Contrôles d’accès au plan de gestion/contrôle | Oui | Contrôle d’accès en fonction du rôle (RBAC) Azure Resource Manager |
| Contrôles d’accès au plan de données (à chaque niveau de service) | Oui | Stratégie d’accès à Key Vault |