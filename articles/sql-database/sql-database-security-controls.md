---
title: Contrôles de sécurité pour Azure SQL Database
description: Check-list des contrôles de sécurité pour l’évaluation d’Azure SQL Database
services: sql-database
author: msmbaldwin
manager: rkalrin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 77ff55389bac53d8719d86b4ac77f281415af49f
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886226"
---
# <a name="security-controls-for-azure-sql-database"></a>Contrôles de sécurité pour Azure SQL Database

Cet article décrit les contrôles de sécurité qui sont intégrés dans Azure SQL Database.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

SQL Database inclut une [base de données unique](sql-database-single-index.yml) et une [instance gérée](sql-database-managed-instance.md). Les entrées suivantes s’appliquent aux deux offres, sauf mention contraire.

## <a name="network"></a>Réseau

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| OUI | S’applique exclusivement à une [base de données unique](sql-database-single-index.yml). |
| Prise en charge de l’injection par Azure Virtual Network| OUI | S’applique exclusivement à une [instance managée](sql-database-managed-instance.md). |
| Prise en charge de l’isolement et du pare-feu réseau| OUI | Pare-feu au niveau de la base de données et du serveur. L’isolement réseau est réservé à une [instance gérée](sql-database-managed-instance.md). |
| Prise en charge du tunneling forcé| OUI | [Instance gérée](sql-database-managed-instance.md) via un VPN [ExpressRoute](../expressroute/index.yml). |

## <a name="monitoring--logging"></a>Supervision et journalisation

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure, comme Log Analytics ou Application Insights| OUI | SecureSphere, la solution SIEM d’Imperva, est également prise en charge à travers l’intégration d’[Azure Event Hubs](../event-hubs/index.yml) via l’[audit SQL](sql-database-auditing.md). |
| Journalisation et audit du plan de contrôle et de gestion| OUI | Oui, seulement pour certains événements |
| Journalisation et audit du plan de données | OUI | Via l’[audit SQL](sql-database-auditing.md) |

## <a name="identity"></a>Identité

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | Azure Active Directory (Azure AD) |
| Authorization| OUI | Aucun |

## <a name="data-protection"></a>Protection des données

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement côté serveur au repos : Clés managées par Microsoft | OUI | Appelé « chiffrement en cours d’utilisation », comme décrit dans l’article [Always Encrypted](sql-database-always-encrypted.md). Le chiffrement côté serveur utilise le [chiffrement transparent des données](transparent-data-encryption-azure-sql.md).|
| Chiffrement en transit :<ul><li>Chiffrement Azure ExpressRoute</li><li>Chiffrement dans un réseau virtuel</li><li>Chiffrement entre réseaux virtuels</ul>| OUI | Utilisation du protocole HTTPS. |
| Gestion des clés de chiffrement, comme CMK ou BYOK| OUI | Une gestion de clé par le service et une gestion de clé par le client sont proposées. Cette dernière est proposée par [Azure Key Vault](../key-vault/index.yml). |
| Chiffrement au niveau des colonnes fourni par les services de données Azure| OUI | Via [Always Encrypted](sql-database-always-encrypted.md). |
| Appels d’API chiffrés| OUI | À l’aide de HTTPS/SSL. |

## <a name="configuration-management"></a>Gestion des configurations

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion des configurations, comme la gestion des versions des configurations| Non  | Aucun |

## <a name="additional-security-controls-for-sql-database"></a>Contrôles de sécurité supplémentaires pour SQL Database

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Préventif : évaluation des vulnérabilités | OUI | Voir [Le service d’évaluation des vulnérabilités SQL vous permet d’identifier des vulnérabilités de base de données](sql-vulnerability-assessment.md). |
| Préventif : découverte et classification des données  | OUI | Voir [Découverte et classification des données pour Azure SQL Database et SQL Data Warehouse](sql-database-data-discovery-and-classification.md) |
| Détection : détection des menaces | OUI | Voir [Advanced Threat Protection pour Azure SQL Database](sql-database-threat-detection-overview.md). |

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en plus sur les [contrôles de sécurité intégrés des services Azure](../security/fundamentals/security-controls.md).
