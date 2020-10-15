---
title: Contrôles de sécurité
description: Check-list des contrôles de sécurité pour l’évaluation d’Azure SQL Database
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: eec032ad56d00778627fc147761f61c03ba8bafd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89442086"
---
# <a name="security-controls-for-azure-sql-database-and-sql-managed-instance"></a>Contrôles de sécurité pour Azure SQL Database et SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Cet article décrit les contrôles de sécurité qui sont intégrés dans Azure SQL Database et Azure SQL Managed Instance.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]



## <a name="network"></a>Réseau

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Oui | S’applique à [SQL Database](../index.yml) uniquement. |
| Prise en charge de l’injection par Azure Virtual Network| Oui | S’applique exclusivement à [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md). |
| Prise en charge de l’isolement et du pare-feu réseau| Oui | Pare-feu au niveau de la base de données et du serveur. L’isolement réseau est réservé à [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md). |
| Prise en charge du tunneling forcé| Oui | [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) via un VPN [ExpressRoute](../expressroute/../index.yml). |

## <a name="monitoring--logging"></a>Supervision et journalisation

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure, comme Log Analytics ou Application Insights| Oui | SecureSphere, la solution SIEM d’Imperva, est également prise en charge à travers l’intégration d’[Azure Event Hubs](../event-hubs/../index.yml) via l’[audit SQL](../../azure-sql/database/auditing-overview.md). |
| Journalisation et audit du plan de contrôle et de gestion| Oui | Oui, seulement pour certains événements |
| Journalisation et audit du plan de données | Oui | Via l’[audit SQL](../../azure-sql/database/auditing-overview.md) |

## <a name="identity"></a>Identité

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentification| Oui | Azure Active Directory (Azure AD) |
| Autorisation| Oui | None |

## <a name="data-protection"></a>Protection de données

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement côté serveur au repos : Clés managées par Microsoft | Oui | Appelé « chiffrement en cours d’utilisation », comme décrit dans l’article [Always Encrypted](always-encrypted-certificate-store-configure.md). Le chiffrement côté serveur utilise le [chiffrement transparent des données](transparent-data-encryption-tde-overview.md).|
| Chiffrement en transit :<ul><li>Chiffrement Azure ExpressRoute</li><li>Chiffrement dans un réseau virtuel</li><li>Chiffrement entre réseaux virtuels</ul>| Oui | Utilisation du protocole HTTPS. |
| Gestion des clés de chiffrement, comme CMK ou BYOK| Oui | Une gestion de clé par le service et une gestion de clé par le client sont proposées. Cette dernière est proposée par [Azure Key Vault](../key-vault/../index.yml). |
| Chiffrement au niveau des colonnes fourni par les services de données Azure| Oui | Via [Always Encrypted](always-encrypted-certificate-store-configure.md). |
| Appels d’API chiffrés| Oui | Utilisation de HTTPS/TLS. |

## <a name="configuration-management"></a>Gestion des configurations

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion des configurations, comme la gestion des versions des configurations| Non  | None |

## <a name="additional-security-controls-for-sql-database"></a>Contrôles de sécurité supplémentaires pour SQL Database

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Préventif : évaluation des vulnérabilités | Oui | Voir [Le service d’évaluation des vulnérabilités SQL vous permet d’identifier des vulnérabilités de base de données](sql-vulnerability-assessment.md). |
| Préventif : découverte et classification des données  | Oui | Voir [Découverte et classification des données pour Azure SQL Database et Azure Synapse Analytics](data-discovery-and-classification-overview.md). |
| Détection : détection des menaces | Oui | Voir [Advanced Threat Protection pour Azure SQL Database](threat-detection-overview.md). |

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en plus sur les [contrôles de sécurité intégrés des services Azure](../../security/fundamentals/security-controls.md).
