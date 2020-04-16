---
title: Contrôles de sécurité pour Azure Key Vault
description: Check-list des contrôles de sécurité pour l’évaluation d’Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: cd6602f68b63e2c236e7f3905d33b88fbda36ed2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427025"
---
# <a name="security-controls-for-azure-key-vault"></a>Contrôles de sécurité pour Azure Key Vault

Cet article décrit les contrôles de sécurité intégrés dans Azure Key Vault. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Réseau

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Oui | Utilisation de points de terminaison de service de réseau virtuel. |
| Prise en charge de l’injection de réseau virtuel| Non |  |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| Oui | Utilisation de règles de pare-feu de réseau virtuel. |
| Prise en charge du tunneling forcé| Non |  |

## <a name="monitoring--logging"></a>Supervision et journalisation

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| Oui | Utilisation de Log Analytics. |
| Journalisation et audit du plan de gestion/contrôle| Oui | Utilisation de Log Analytics. |
| Journalisation et audit du plan de données| Oui | Utilisation de Log Analytics. |

## <a name="identity"></a>Identité

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentification| Oui | L’authentification est effectuée via Azure Active Directory. |
| Autorisation| Oui | Utilisation d’une stratégie d’accès à Key Vault. |

## <a name="data-protection"></a>Protection de données

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement côté serveur au repos : Clés managées par Microsoft | Oui | Tous les objets sont chiffrés. |
| Chiffrement côté serveur au repos : clés gérées par le client (BYOK) | Oui | Le client contrôle toutes les clés de son Key Vault. Lorsque des clés sauvegardées avec HSM (Hardware Security Module) sont spécifiées, un module HSM Niveau 2 FIPS protège la clé, le certificat ou le secret. |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A |  |
| Chiffrement en transit (comme ExpressRoute, de réseau virtuel et de réseau virtuel à réseau virtuel)| Oui | Toutes les communications se font par le biais d’appels d’API chiffrés |
| Appels d’API chiffrés| Oui | Utilisation du protocole HTTPS. |

## <a name="access-controls"></a>Contrôles d’accès

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Contrôles d’accès au plan de gestion/contrôle | Oui | Contrôle d’accès en fonction du rôle (RBAC) Azure Resource Manager |
| Contrôles d’accès au plan de données (à chaque niveau de service) | Oui | Stratégie d’accès à Key Vault |

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en plus sur les [contrôles de sécurité intégrés des services Azure](../../security/fundamentals/security-controls.md).