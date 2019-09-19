---
title: Contrôles de sécurité pour le Stockage Azure
description: Check-list des contrôles de sécurité pour l’évaluation du Stockage Azure
services: storage
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.service: storage
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 8cb14c19e8816d53c7d9385563f916bee5d4a6af
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886222"
---
# <a name="security-controls-for-azure-storage"></a>Contrôles de sécurité pour le Stockage Azure

Cet article décrit les contrôles de sécurité intégrés au Stockage Azure. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Protection des données

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement côté serveur au repos : Clés managées par Microsoft | OUI |  |
| Chiffrement côté serveur au repos : clés gérées par le client (BYOK) | OUI | Consultez [Chiffrement du service de stockage à l’aide de clés gérées par le client dans Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A |  |
| Chiffrement en transit (comme ExpressRoute, de réseau virtuel et de réseau virtuel à réseau virtuel)| OUI | Prend en charge les mécanismes HTTPS/TLS standards.  Les utilisateurs peuvent également chiffrer les données avant leur transmission au service. |
| Appels d’API chiffrés| OUI |  |

## <a name="network"></a>Réseau

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| OUI |  |
| Prise en charge de l’injection de réseau virtuel| N/A |  |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI | |
| Prise en charge du tunneling forcé| N/A |  |

## <a name="monitoring--logging"></a>Supervision et journalisation

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | Indicateurs de performance Azure Monitor disponible à présent, début de la préversion des journaux d’activité |
| Journalisation et audit du plan de gestion et de contrôle | OUI | Journal d’activité Azure Resource Manager |
| Journalisation et audit du plan de données| OUI | Journaux de diagnostic de service et début de la préversion de la journalisation Azure Monitor  |

## <a name="identity"></a>Identité

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | Azure Active Directory, clé partagée, jeton d’accès partagé. |
| Authorization| OUI | Autorisation de support via le contrôle d’accès en fonction du rôle (RBAC), les listes de contrôle d'accès POSIX et les jetons SAS |

## <a name="configuration-management"></a>Gestion des configurations

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI | Prise en charge du contrôle de version du fournisseur de ressources via les API Azure Resource Manager |

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en plus sur les [contrôles de sécurité intégrés des services Azure](../../security/fundamentals/security-controls.md).