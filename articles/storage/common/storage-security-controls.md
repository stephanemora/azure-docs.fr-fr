---
title: Contrôles de sécurité
titleSuffix: Azure Storage
description: Affichez les listes de contrôle de sécurité pour l’évaluation du stockage Azure. Les domaines couverts sont la protection des données, le réseau, la surveillance et la journalisation, l’identité et la configuration.
services: storage
author: msmbaldwin
ms.author: mbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 3b4d74e7ba869e0438a936817d824e841823d472
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91715712"
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
| Prise en charge des balises de service| Oui | Pour plus d’informations sur les balises de service prises en charge par Stockage Azure, consultez [Vue d’ensemble des balises de service Azure](../../virtual-network/service-tags-overview.md). |
| Prise en charge de l’injection de réseau virtuel| N/A |  |
| Prise en charge de l’isolement et du pare-feu réseau| Oui | |
| Prise en charge du tunneling forcé| N/A |  |

## <a name="monitoring--logging"></a>Supervision et journalisation

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| Oui | Métriques Azure Monitor|
| Journalisation et audit du plan de gestion et de contrôle | Oui | Journaux d’activité |
| Journalisation et audit du plan de données| Oui | Journaux de ressources Azure Monitor |

## <a name="identity"></a>Identité

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentification| Oui | Azure Active Directory, clé partagée, jeton d’accès partagé. |
| Autorisation| Oui | Autorisation de support via le contrôle d’accès en fonction du rôle (Azure RBAC), les listes de contrôle d'accès POSIX et les jetons SAP |

## <a name="configuration-management"></a>Gestion des configurations

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| Oui | Prise en charge du contrôle de version du fournisseur de ressources via les API Azure Resource Manager |

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en plus sur les [contrôles de sécurité intégrés des services Azure](../../security/fundamentals/security-controls.md).