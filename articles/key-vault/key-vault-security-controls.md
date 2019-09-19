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
ms.openlocfilehash: d7f92a309a3d0b5d8e85f1e270d5590f46496a77
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886254"
---
# <a name="security-controls-for-azure-key-vault"></a>Contrôles de sécurité pour Azure Key Vault

Cet article décrit les contrôles de sécurité intégrés dans Azure Key Vault. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Réseau

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| OUI | Utilisation de points de terminaison de service de réseau virtuel. |
| Prise en charge de l’injection de réseau virtuel| Non |  |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI | Utilisation de règles de pare-feu de réseau virtuel. |
| Prise en charge du tunneling forcé| Non |  |

## <a name="monitoring--logging"></a>Supervision et journalisation

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | Utilisation de Log Analytics. |
| Journalisation et audit du plan de gestion/contrôle| OUI | Utilisation de Log Analytics. |
| Journalisation et audit du plan de données| OUI | Utilisation de Log Analytics. |

## <a name="identity"></a>Identité

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | L’authentification est effectuée via Azure Active Directory. |
| Authorization| OUI | Utilisation d’une stratégie d’accès à Key Vault. |

## <a name="data-protection"></a>Protection des données

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement côté serveur au repos : Clés managées par Microsoft | OUI | Tous les objets sont chiffrés. |
| Chiffrement côté serveur au repos : clés gérées par le client (BYOK) | OUI | Le client contrôle toutes les clés de son Key Vault. Lorsque des clés sauvegardées avec HSM (Hardware Security Module) sont spécifiées, un module HSM Niveau 2 FIPS protège la clé, le certificat ou le secret. |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A |  |
| Chiffrement en transit (comme ExpressRoute, de réseau virtuel et de réseau virtuel à réseau virtuel)| OUI | Toutes les communications se font par le biais d’appels d’API chiffrés |
| Appels d’API chiffrés| OUI | Utilisation du protocole HTTPS. |

## <a name="access-controls"></a>Contrôles d’accès

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Contrôles d’accès au plan de gestion/contrôle | OUI | Contrôle d’accès en fonction du rôle (RBAC) Azure Resource Manager |
| Contrôles d’accès au plan de données (à chaque niveau de service) | OUI | Stratégie d’accès à Key Vault |

## <a name="next-steps"></a>Étapes suivantes

- Découvrez plus d’informations sur les [contrôles de sécurité intégrés des services Azure](../security/fundamentals/security-controls.md).