---
title: Contrôles de sécurité pour le Stockage Azure
description: Check-list des contrôles de sécurité pour l’évaluation du Stockage Azure
services: storage
author: msmbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 379acaf48c02f0a579c07773cd48366d962a44f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061117"
---
# <a name="security-controls-for-azure-storage"></a>Contrôles de sécurité pour le Stockage Azure

Cet article décrit les contrôles de sécurité intégrés au Stockage Azure. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Protection de données

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement côté serveur au repos : Clés managées par Microsoft | Oui |  |
| Chiffrement côté serveur au repos : clés gérées par le client (BYOK) | Oui | Consultez [Chiffrement du service de stockage à l’aide de clés gérées par le client dans Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A |  |
| Chiffrement en transit (comme ExpressRoute, de réseau virtuel et de réseau virtuel à réseau virtuel)| Oui | Prend en charge les mécanismes HTTPS/TLS standards.  Les utilisateurs peuvent également chiffrer les données avant leur transmission au service. |
| Appels d’API chiffrés| Oui |  |

## <a name="network"></a>Réseau

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Oui |  |
| Prise en charge de l’injection de réseau virtuel| N/A |  |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| Oui | |
| Prise en charge du tunneling forcé| N/A |  |

## <a name="monitoring--logging"></a>Supervision et journalisation

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| Oui | Métriques Azure Monitor|
| Journalisation et audit du plan de gestion et de contrôle | Oui | Journal d’activité Azure Resource Manager |
| Journalisation et audit du plan de données| Oui | Journaux de diagnostic du service.|

## <a name="identity"></a>Identité

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentification| Oui | Azure Active Directory, clé partagée, jeton d’accès partagé. |
| Autorisation| Oui | Autorisation de support via le contrôle d’accès en fonction du rôle (RBAC), les listes de contrôle d'accès POSIX et les jetons SAS |

## <a name="configuration-management"></a>Gestion des configurations

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| Oui | Prise en charge du contrôle de version du fournisseur de ressources via les API Azure Resource Manager |

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en plus sur les [contrôles de sécurité intégrés des services Azure](../../security/fundamentals/security-controls.md).