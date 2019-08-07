---
title: Attributs de sécurité pour Azure Key Vault
description: Une liste de contrôle des attributs de sécurité pour l’évaluation d’Azure Key Vault
services: key-vault
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 836d025c5bc69da9606c9a6172ac6a43caaaf29b
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444023"
---
# <a name="security-attributes-for-azure-key-vault"></a>Attributs de sécurité pour Azure Key Vault

Cet article décrit les attributs de sécurité intégrés à Azure Key Vault. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos (comme le chiffrement côté serveur, le chiffrement côté serveur avec clés managées par le client et d’autres fonctions de chiffrement)| OUI | Tous les objets sont chiffrés. |
| Chiffrement en transit (comme ExpressRoute, de réseau virtuel et de réseau virtuel à réseau virtuel)| OUI | Toutes les communications se font par le biais d’appels d’API chiffrés |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| OUI | Le client contrôle toutes les clés de son Key Vault. Lorsque des clés sauvegardées avec HSM (Hardware Security Module) sont spécifiées, un module HSM Niveau 2 FIPS protège la clé, le certificat ou le secret. |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A |  |
| Appels d’API chiffrés| OUI | Utilisation du protocole HTTPS. |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| OUI | Utilisation de points de terminaison de service de réseau virtuel. |
| Prise en charge de l’injection de réseau virtuel| Non |  |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI | Utilisation de règles de pare-feu de réseau virtuel. |
| Prise en charge du tunneling forcé| Non |  |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | Utilisation de Log Analytics. |

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | L’authentification est effectuée via Azure Active Directory. |
| Authorization| OUI | Utilisation d’une stratégie d’accès à Key Vault. |


## <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion/contrôle| OUI | Utilisation de Log Analytics. |
| Journalisation et audit du plan de données| OUI | Utilisation de Log Analytics. |

## <a name="access-controls"></a>Contrôles d’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Contrôles d’accès au plan de gestion/contrôle | OUI | Contrôle d’accès en fonction du rôle (RBAC) Azure Resource Manager |
| Contrôles d’accès au plan de données (à chaque niveau de service) | OUI | Stratégie d’accès à Key Vault |